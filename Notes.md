# Android Jetpack Notes

## Data Binding

DataBinding Syntax
Enable Data Binding:

```bash
dataBinding: {
	enabled: true
}
```

```bash
private lateinit var binding: ActivityMainBinding
```

Data binding layout file referencing object

```bash
<layout>
	<data>
		<variable
			name="Any name"
			type="DataClassName" />
	</data>
</layout>
```

## ViewModel

Model for a view which is designed to store UI related data
ViewModel Dependencies:

https://developer.android.com/jetpack/androidx/releases/lifecycle

Create ViewModel and extend ViewModel to class

```bash
private lateinit var viewModel: MainActivityViewModel
viewModel = ViewModelProvider(this).get(MainActivityViewModel::class.java)
```

VIewModelFactory for setting value in viewmodel class

```bash
class MainActivityViewModelFactory(private val startingTotal: Int): ViewModelProvider.Factory {
    override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(MainActivityViewModel::class.java)) {
            return MainActivityViewModel(startingTotal) as T
        }
        throw IllegalArgumentException("Unknown View Model Class")
    }
}
```

In ViewModelClass

```bash
class MainActivityViewModel(startingTotal: Int): ViewModel() {
    private var count = 0

    init {
        count = startingTotal
    }
```

In MainActivity

```bash
videModelFactory = MainActivityViewModelFactory(100)viewModel = ViewModelProvider(this, videModelFactory).get(MainActivityViewModel::class.java)
```

## LiveData

/_ No get data method required _/

```bash
viewModel.count.observe(this, Observer {
            binding.countText.text = it.toString()
})
```

Data Encapsulation in viewmodel class

```bash
private var count = MutableLiveData<Int>()
    val totalData: LiveData<Int>

    get() = count

    init {
        count.value = startingTotal
    }
```

Give Lifecycle owner while using livedata with databinding

```bash
binding.lifecycleOwner = this

/* For 2 way data binding */

@={String.valueOf(viewModel.totalData)}
```

## Navigation android

Dependencies:
https://developer.android.com/jetpack/androidx/releases/navigation

Create nav_graph.xml file and in main activity add NavHostFragment Container It will automatically recognize.

for navigating to other fragments

```bash
it.findNavController().navigate(R.id.action_homeFragment_to_secondFragment)
```

For sending data to another screen

```bash
val bundle = bundleOf("user_input" to editTextTextPersonName.text.toString())
it.findNavController().navigate(R.id.action_homeFragment_to_secondFragment, bundle)
```
