# curve-contract/tests

Test cases for Curve pools.

## Subdirectories

* [`fixtures`](fixtures): Pytest fixtures](https://docs.pytest.org/en/latest/fixture.html)
* [`pools`](pools): Tests for [pool](../contracts/pools) contracts
* [`token`](token): Tests for [LP token](../contracts/tokens) contracts

## Files

* [`conftest.py`](conftest.py): Base configuration file for the test suite.
* [`simulation.py`](simulation.py): A python model of the math used within Curve's contracts. Used for testing expected outcomes with actual results.

## Organization

* Tests are organized by general category, then split between unitary and integration tests.
* Common tests for all pools are located in [`tests/common`](common).
* Valid pool names are the names of the subdirectories within [`contracts/pools`](../contracts/pools).

## Running the tests

To run the entire suite:

```bash
brownie test
```

To run tests on a specific pool:

```bash
pytest tests --pool <POOL NAME>
```

To run against one of the templates, use `template-base` or `template-y`.

You can optionally include the `--coverage` flag to view a coverage report upon completion of the tests.

## Fixtures

Test fixtures are located within the [`tests/fixtures`](fixtures) subdirectory. New fixtures should be added here instead of within the base [`conftest.py`](conftest.py).

All fixtures are [documented](fixtures/README.md) within the fixtures subdirectory readme.

## Markers

We use the following custom [markers](https://docs.pytest.org/en/stable/example/markers.html) to parametrize common tests across different pools:

### `skip_pool(*pools)`
Exclude one or more pools from the given test.

```python
@pytest.mark.skip_pool("compound", "usdt", "y")
def test_only_some_pools(swap):
    ...
```

### `target_pool(*pools)`

Only run the given test against one or more pools specified in the marker.

```python
@pytest.mark.target_pool("ren", "sbtc")
def test_btc_pools(swap):
    ...
```

### `lending`

Only run the given test against pools that involve lending.

```python
@pytest.mark.lending
def test_underlying(swap):
    ...
```

### `itercoins(*arg)`
Parametrizes each of the given arguments with a range of numbers equal to the total number of coins for the given pool. When multiple arguments are given, each argument has a unique value for every generated test.

For example, `itercoins("send", "recv")` with a pool of 3 coins will parametrize with the sequence `[(0, 1), (0, 2), (1, 0), (1, 2), (2, 0), (2, 1)]`.

```python
@pytest.mark.itercoins("send", "recv"):
def test_swap(accounts, swap, send, recv):
    swap.exchange(send, recv, 0, 0, {'from': accounts[0]})
```
