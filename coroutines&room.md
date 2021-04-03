# Room and Coroutines Notes

#### Dependencies required with room

```bash
def room_version = "2.2.6"
implementation "androidx.room:room-runtime:$room_version"
kapt "androidx.room:room-compiler:$room_version"
implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.4.3'
implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.4.3'
```

#### Then create data class which acts as a table in database

```bash
@Entity(tableName = "subscriber_data_table")
data class Subscriber (

    @PrimaryKey(autoGenerate = true)
    @ColumnInfo(name="subscriber_id")
    val id: Int,

    @ColumnInfo(name="subscriber_name")
    val name: String,

    @ColumnInfo(name="subscriber_email")
    val email: String
)
```

#### Next is DAO (Data Access Object) interface. Here we create methods which helps in accessing data from SQLite Database

###### Room doesn't support database access on main thread. It may block thread for long time.

```bash
@Dao
interface SubscriberDAO {

    @Insert
    suspend fun insertSubscriber(subscriber: Subscriber): Long

    @Update
    suspend fun updateSubscriber(subscriber: Subscriber)

    @Delete
    suspend fun deleteSubscriber(subscriber: Subscriber)

    @Query("DELETE FROM subscriber_data_table")
    suspend fun deleteAll()

    @Query("SELECT * FROM subscriber_data_table")
    fun getAllSubscribers(): LiveData<List<Subscriber>>
}
```

#### Next we have to create DatabaseClass where we create singleton instance of database which can be used to access database anywhere in the app

```bash
@Database(entities = [Subscriber::class], version = 1)
abstract class SubscriberDatabase : RoomDatabase() {

    abstract val subscriberDAO: SubscriberDAO

    companion object {
        @Volatile
        private var INSTANCE: SubscriberDatabase? = null
        fun getInstance(context: Context): SubscriberDatabase {
            synchronized(this) {
                var instance = INSTANCE
                if (instance == null) {
                    instance = Room.databaseBuilder(
                        context.applicationContext,
                        SubscriberDatabase::class.java,
                        "subscriber_data_database"
                    ).build()
                }
                return instance
            }
        }
    }

}
```

#### If you wish to follow MVVM Architecture. Make DatabaseRepository class which acts as a mediator between Model and ViewModel

```bash
class SubscriberRepository(private val dao: SubscriberDAO) {

    val subscribers = dao.getAllSubscribers()

    suspend fun insert(subscriber: Subscriber) {
        dao.insertSubscriber(subscriber)
    }

    suspend fun update(subscriber: Subscriber) {
        dao.updateSubscriber(subscriber)
    }

    suspend fun delete(subscriber: Subscriber) {
        dao.deleteSubscriber(subscriber)
    }

    suspend fun deleteAll() {
        dao.deleteAll()
    }

}
```

## Use Event Wrapper class if you want to communicate from ViewModel to the view. Link is

https://medium.com/androiddevelopers/livedata-with-snackbar-navigation-and-other-events-the-singleliveevent-case-ac2622673150

#### Next create viewmodel where we can perform actions

#### For optimized recycler view

###### Adapter Code

```bash
class MyRecyclerViewAdapter( private val clickListener: (Subscriber) -> Unit): RecyclerView.Adapter<MyViewHolder>() {

    private val subscribers = ArrayList<Subscriber>()

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MyViewHolder {
        val layoutInflater = LayoutInflater.from(parent.context)
        val binding: ListItemBinding = DataBindingUtil.inflate(layoutInflater, R.layout.list_item, parent, false)
        return MyViewHolder(binding)
    }

    override fun getItemCount(): Int {
        return subscribers.size
    }

    override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
        holder.bind(subscribers[position], clickListener)
    }

    fun setList(subscribersList: List<Subscriber>) {
        subscribers.clear()
        subscribers.addAll(subscribersList)
    }
}

class MyViewHolder(val binding:ListItemBinding): RecyclerView.ViewHolder(binding.root) {
    fun bind(subscriber: Subscriber, clickListener: (Subscriber) -> Unit) {
        binding.nameTextView.text = subscriber.name
        binding.emailTextView.text = subscriber.email
        binding.listItemLayout.setOnClickListener {
            clickListener(subscriber)
        }
    }
}
```

###### Main activity. Here we initialize adapter and pass layout manager 1 time and for changes we observe livedata and update setList function

```bash
binding.subscriberRecyclerview.layoutManager = LinearLayoutManager(this)
adapter = MyRecyclerViewAdapter({selectedItem: Subscriber -> listItemClicked(selectedItem)})
binding.subscriberRecyclerview.adapter = adapter

// For observing live data and updating set list functon

subscriberViewModel.subscribers.observe(this, Observer {
            Log.i(TAG, it.toString())
            adapter.setList(it)
})

```
