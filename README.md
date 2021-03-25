# assert4hs-tasty

assert4hs provider for tasty

### Example

```haskell

data Foo = Foo {name :: String, age :: Int} deriving (Show, Eq)

isSuitableForEmployment :: Assertion Foo
isSuitableForEmployment =
  simpleAssertion (\a -> age a > 17) (\a -> "new employee must be 18 years or older, but it has " <> show (age a))
    . simpleAssertion (\a -> age a < 70) (\a -> "must be younger than 70 years old, but it has " <> show (age a))

unitTests :: TestTree
unitTests =
  testGroup
    "Unit tests"
    [ 
      fluentTestCase "chaining assertions" $ do
        let result = 4
        assertThat result $
          isGreaterThan 5
            . isLowerThan 20,
      fluentTestCase "focusing on part of data structure" $ do
        assertThat (Foo "someName" 15) $
          isEqualTo (Foo "someName" 15)
            . focus age
            . isGreaterThan 20
            . isLowerEqualThan 5,
      fluentTestCase "Changing subject uder test" $ do
        assertThat (Foo "someName" 15) $
          inside age (isGreaterThan 20 . isLowerEqualThan 5)
            . focus name
            . isEqualTo "someName1",
      fluentTestCase "Tagging assertions" $ do
        assertThat (Foo "someName" 15) $
          inside age (tag "age" . isGreaterThan 20 . isLowerEqualThan 5)
            . tag "name"
            . focus name
            . isEqualTo "someName1"
            . tag "should not be equal"
            . isNotEqualTo "someName",
      fluentTestCase "Custom assertions" $ do
        assertThat (Foo "someName" 15) isSuitableForEmployment,
        fluentTestCase "Custom assertions" $ do
        assertThat (Foo "someName" 76) isSuitableForEmployment
    ]

>>> Progress 1/2: assert4hsTests
>>>   Unit tests
>>>     chaining assertions:                FAIL
>>>       (test/Spec.hs:46): 
>>>       given 4 should be greater than 5
>>>     passed: 1, failed: 1, total: 2
>>>     focusing on part of data structure: FAIL
>>>       (test/Spec.hs:52): 
>>>       given 15 should be greater than 20
>>>       
>>>       (test/Spec.hs:53): 
>>>       given 15 should be lower or equal to 5
>>>     passed: 1, failed: 2, total: 3
>>>     Changing subject uder test:         FAIL
>>>       (test/Spec.hs:56): 
>>>       given 15 should be greater than 20
>>>       
>>>       (test/Spec.hs:56): 
>>>       given 15 should be lower or equal to 5
>>>       
>>>       (test/Spec.hs:58): 
>>>       given "someName" should be equal to "someName1"
>>>       "someName"
>>>       ╷
>>>       │
>>>       ╵
>>>       "someName1"
>>>                ▲
>>>     passed: 0, failed: 3, total: 3
>>>     Tagging assertions:                 FAIL
>>>       (test/Spec.hs:61): 
>>>       [age] given 15 should be greater than 20
>>>       
>>>       (test/Spec.hs:61): 
>>>       [age] given 15 should be lower or equal to 5
>>>       
>>>       (test/Spec.hs:64): 
>>>       [name] given "someName" should be equal to "someName1"
>>>       "someName"
>>>       ╷
>>>       │
>>>       ╵
>>>       "someName1"
>>>                ▲
>>>       (test/Spec.hs:66): 
>>>       [name.should not be equal] given "someName" should be not equal to "someName"
>>>     passed: 0, failed: 4, total: 4
>>>     Custom assertions:                  FAIL
>>>       (test/Spec.hs:35): 
>>>       new employee must be 18 years or older, but it has 15
>>>     passed: 1, failed: 1, total: 2
>>>     Custom assertions:                  FAIL
>>>       (test/Spec.hs:36): 
>>>       must be younger than 70 years old, but it has 76
>>>     passed: 1, failed: 1, total: 2
>>> 
>>> 6 out of 6 tests failed (0.01s)
```