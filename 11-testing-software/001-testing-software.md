Broadly speaking, assertions fall into three categories:

- A *precondition* is something that must be true at the start of a function in order for it to work correctly. For example, a function might check that the list it has been given has at least two elements and that all of its elements are integers.
- A *postcondition* is something that the function guarantees is true when it finishes. For example, a function could check that the value being returned is an integer that is greater than zero, but less than the length of the input list.
- An *invariant* is something that is true for every iteration in a loop. The invariant might be a property of the data (as in the example above), or it might be something like, “the value of highest is less than or equal to the current loop index.”


##  Testing Floating-Point Values

how should we write tests when we don’t know precisely what the right answer is? The best approach is to write tests that check if the actual value is within some tolerance of the expected value. The tolerance can be expressed as the absolute error, which is the absolute value of the difference between two, or the relative error, which the ratio of the absolute error to the value we’re approximating.

For test_alpha, we might decide that an absolute error of 0.01 in the estimation of alpha is acceptable. If we are using pytest, we can check that values lie within this tolerance using pytest.approx:

```python
from collections import Counter

import pytest
import numpy as np

import plotcounts
import countwords


def test_alpha():
    """Test the calculation of the alpha parameter.

    The test word counts satisfy the relationship,
      r = cf**(-1/alpha), where
      r is the rank,
      f the word count, and
      c is a constant of proportionality.

    To generate test word counts for an expected alpha of
      1.0, a maximum word frequency of 600 is used
      (i.e. c = 600 and r ranges from 1 to 600)
    """
    max_freq = 600
    counts = np.floor(max_freq / np.arange(1, max_freq + 1))
    actual_alpha = plotcounts.get_power_law_params(counts)
    expected_alpha = pytest.approx(1.0, abs=0.01)
    assert actual_alpha == expected_alpha


def test_word_count():
        """Test the counting of words.

    The example poem is Risk, by Anaïs Nin.
    """
    risk_poem_counts = {'the': 3, 'risk': 2, 'to': 2, 'and': 1,
      'then': 1, 'day': 1, 'came': 1, 'when': 1, 'remain': 1,
      'tight': 1, 'in': 1, 'a': 1, 'bud': 1, 'was': 1,
      'more': 1, 'painful': 1, 'than': 1, 'it': 1, 'took': 1,
      'blossom': 1}
    expected_result = Counter(risk_poem_counts)
    with open('test_data/risk.txt', 'r') as reader:
        actual_result = countwords.count_words(reader)
    assert actual_result == expected_result
```
## Integration Testing
Our Zipf’s Law analysis has two steps: counting the words in a text and estimating the  
alpha parameter from the word count. Our unit tests give us some confidence that these components work in isolation, but do they work correctly together? Checking that is called integration testing.

Integration tests are structured the same way as unit tests: a fixture is used to produce an actual result that is compared against the expected result. However, creating the fixture and running the code can be considerably more complicated

## 11.6 Regression Testing
So far we have tested two simplified texts: a short poem and a collection of random words with a known frequency distribution. The next step is to test with real data, i.e., an actual book. The problem is, we don’t know the expected result: it’s not practical to count the words in Dracula by hand, and even if we tried, the odds are good that we’d make a mistake.
For this kind of situation we can use regression testing. Rather than assuming that the test’s author knows what the expected result should be, regression tests compare today’s answer with a previous one. This doesn’t guarantee that the answer is right—if the original answer is wrong, we could carry that mistake forward indefinitely—but it does draw attention to any changes (or “regressions”).
