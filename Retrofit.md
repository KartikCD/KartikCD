# Retrofit With kotlin

#### Libraries required:

1. Retrofit
2. GSON
3. OkHTTP3 Logging interceptor

#### Album Data Class to parse data

```bash
class Album : ArrayList<AlbumItem>()

data class AlbumItem(
    val id: Int,
    val title: String,
    val userId: Int
)
```

#### Then we require interface to create abstract method which helps in accessing data

```bash
interface AlbumService {

    @GET("/albums")
    suspend fun getAlbums(): Response<Album>
}
```

#### Next create a singleto object to access instance of retrofit to all files

```bash
class RetrofitInstance {
    companion object {
        val BASE_URL = "https://jsonplaceholder.typicode.com"

        fun getRetrofitInstance(): Retrofit {
            return Retrofit.Builder()
                .baseUrl(BASE_URL)
                .addConverterFactory(GsonConverterFactory.create(GsonBuilder().create()))
                .build();
        }
    }
}
```

#### In main activity to access data with livedata

```bash
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val retService = RetrofitInstance.getRetrofitInstance().create(AlbumService::class.java)

        val responseLiveData: LiveData<Response<Album>> = liveData {
            val response = retService.getAlbums()
                emit(response)
        }

        responseLiveData.observe(this, Observer {
            val albumList = it.body()?.listIterator()
            if (albumList!=null) {
                while (albumList.hasNext()) {
                    val albumItem = albumList.next()
                    Log.i("Retrofits", albumItem.title)
                }
            }
        })

    }
}
```
