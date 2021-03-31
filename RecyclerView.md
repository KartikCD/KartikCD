# Recycler View Code

#### RecyclerView Adapter Class Code

```bash
class MyRecyclerViewAdapter(private val fruitsList: List<Fruit>, private val clickListener: (Fruit) -> Unit): RecyclerView.Adapter<MyViewHolder>() {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MyViewHolder {
        val layoutInflater = LayoutInflater.from(parent.context)
        val listItem = layoutInflater.inflate(R.layout.list_item, parent, false)
        return MyViewHolder(listItem)
    }

    override fun getItemCount(): Int {
        return fruitsList.size
    }

    override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
        holder.bind(fruitsList[position], clickListener)

    }

}

class MyViewHolder(val view: View): RecyclerView.ViewHolder(view) {
    fun bind(fruit: Fruit, clickListener: (Fruit) -> Unit) {
        view.textView.text = fruit.name
        view.setOnClickListener {
            clickListener(fruit)
        }
    }
}
```

#### Main Activity Code

```bash
class MainActivity : AppCompatActivity() {

    val fruitsList = listOf(Fruit("Mango"), Fruit("Apple"), Fruit("Banana"), Fruit("Cherry"), Fruit("Watermelon"), Fruit("Lemon"), Fruit("Pear"), Fruit("Orange"), Fruit("Kiwi"), Fruit("Jackfruit"))

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        my_recyclerview.setBackgroundColor(Color.YELLOW)
        my_recyclerview.layoutManager = LinearLayoutManager(this)
        my_recyclerview.adapter = MyRecyclerViewAdapter(fruitsList, {selectedFruitItem: Fruit -> listItemClicked(selectedFruitItem)})
    }

    private fun listItemClicked(fruit: Fruit) {
        Log.i("Kartik", "listItemClicked: "+fruit.name)
        Toast.makeText(this,"Name of fruit is " + fruit.name, Toast.LENGTH_LONG).show()
    }
}
```
