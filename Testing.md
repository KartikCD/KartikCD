# Testing Notes

#### JUNIT is the most popular testing framework for java and kotlin applications

#### Espresso is UI testing framework specially created for android

#### TestDoubles Types

1. Fakes
2. Stubs
3. Mocks

#### Testing MyCacl class

```bash
/* MyCalc Class implementation */

class MyCalc : Calculations {

    private val pi = 3.14

    override fun calculateCircumference(redius: Double): Double {
        return 2 * pi * redius
    }

    override fun calculateArea(redius: Double): Double {
        return pi * redius * redius
    }
}

/* MyCalcTest class Implementation */
class MyCalcTest {

    private lateinit var myCalc: MyCalc

    @Before
    fun setUp() {
        myCalc = MyCalc()
    }

    @Test
    fun calculateCircumference_radiusGiven_returnsCorrectResult() {
        val result = myCalc.calculateCircumference(2.1)
        assertThat(result).isEqualTo(13.188)
    }

    @Test
    fun calculateCircumference_zeroRadius_returnsCorrectResult() {
        val result = myCalc.calculateCircumference(0.0)
        assertThat(result).isEqualTo(0.0)
    }

    @Test
    fun calculateArea_radiusGiven_returnsCorrectResult() {
        val result = myCalc.calculateArea(2.9)
        assertThat(result).isEqualTo(26.4074)
    }

    @Test
    fun calculateArea_zeroRadius_returnsCorrectResult() {
        val result = myCalc.calculateArea(0.0)
        assertThat(result).isEqualTo(0.0)
    }

}
```
