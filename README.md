# NSpek
A tool for testing with syntax similar to Spek, but shorter.
Test different nested scenarios without any boilerplate code.

It is inspired by original idea from:
https://github.com/langara/USpek

### Example
```kotlin
@RunWith(NSpekRunner::class)
class MyTest {
    @Test
    fun NSpekMethodContext.`basic nspek tests`() {
    
        "create some mutable list" o {

            val list = mutableListOf(0, 1, 2)

            assertEquals(3, list.size)

            "check list details" o {
                assertEquals(0, list[0])
                assertEquals(1, list[1])
                assertEquals(2, list[2])
            }

            "add some elements to the list" o {
                list.add(3)
                list.add(4)

                assertEquals(3, list[3])
                assertEquals(4, list[4])
                assertEquals(5, list.size)
            }

            "remove middle element from the list" o {
                list.removeAt(1)

                "try to check not existing element - it should fail" o {
                    assertEquals(2, list[2])
                }

                // this will still work even when the sub test above fails
                "correctly check the list after removing middle element" o {
                    assertEquals(2, list.size)
                    assertEquals(0, list[0])
                    assertEquals(2, list[1])
                }

                "use custom assertion to generate some error" o {
                    list.size eq 666 // it should report error with correct line number
                }
            }
        }
    }
}
```

### Building with JitPack
```gradle
    repositories {
        maven { url "https://jitpack.io" }
    }
   
    dependencies {
        testImplementation 'com.github.elpassion:NSpek:master-SNAPSHOT'
    }
```

JitPack: https://jitpack.io/#elpassion/NSpek

### Details:

For each test method a new instance of the test class will be created.
We belive that tests isolation should be by default, but if you really need to share some
objects between tests you can create them outside of the test class.

Also if you need at any point to perform a tear down action, you have to wrap your code with try finally, example:

```
try {
    "add some elements to the list" o {
        list.add(4)

        assertEquals(5, list.size)
    }
} finally {
    // tear down action
}
```

