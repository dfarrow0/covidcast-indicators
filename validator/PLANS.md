# Validator checks and features

## Current checks for indicator source data

* Appropriate file name
* Recognized geographical type (county, state, etc)
* Recognized geo id format (e.g. state is two lowercase letters)
* Missing geo type + signal + date combos based on the geo type + signal combos Covidcast metadata says should be available
* Missing ‘val’ values
* Negative ‘val’ values
* Out-of-range ‘val’ values (>0 for all signals, <=100 for percents, <=100 000 for proportions)
* Missing ‘se’ values
* Appropriate ‘se’ values, within a calculated reasonable range
* Stderr != 0
* If signal and stderr both = 0 (seen in Quidel data due to lack of Jeffreys correction, [issue 255](https://github.com/cmu-delphi/covidcast-indicators/issues/255#issuecomment-692196541))
* Missing ‘sample_size’ values
* Appropriate ‘sample_size’ values, ≥ 100 (default) or user-defined threshold
* Similar number of obs per day as recent API data
* Similar average values as API data
* Missing dates within the selected range

## Current features

* Errors are summarized in class attribute
* Various check settings are controllable via indicator-specific params.json files

## Checks + features wishlist, and problems to think about:

* check for large jumps
* Which, if any, specific geo_ids are missing
* different thresholds for different files?
* flags to disable certain checks
* re-adjust thresholds to not pass for some previous days that did have issues
* check number of observations
* tests
* check for duplicate rows
* Backfill problems, especially with JHU and USA Facts, where a change to old data results in a datapoint that doesn’t agree with surrounding data ([JHU examples](https://delphi-org.slack.com/archives/CF9G83ZJ9/p1600729151013900)) or is very different from the value it replaced
* Data correctness and consistency over longer time periods (weeks to months)
  * Long-term trends
  * Currently, checks look at a data window of a few days
  * Ryan’s [correlation notebook](https://github.com/cmu-delphi/covidcast/tree/main/R-notebooks) for ideas
  * E.g. Doctor visits decreasing correlation with cases
  * E.g. WY/RI missing or very low compared to historical
* Use hypothesis testing p-values to decide when to raise error or not, instead of static thresholds. Many low but non-significant p-values will also raise error.
* Order raised exceptions by p-value, correcting for multiple testing
* Nicer formatting for error “report”
* Have separate error report sections for data validation checks (which are boolean) and statistical checks, where we want to present the most serious and significant issues first