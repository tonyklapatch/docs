When configuring the availability for [Shipping Methods](../Shipping_Methods) or [Payment Methods](../Payment_Methods), as well as the price-per-country in the Country-based shipping methods, you can enter a list of countries to apply it to.

[TOC]

## Basic Rules

The countries list is always a comma-separated list of 2-character country codes. 

Note that the maximum length of the countries field is 250 characters.

Whitespace is ignored and both the provided country and list of countries are normalised to uppercase. 

### Excluding countries (v1.1+)

Excluding a country is done by prefixing its country code with a dash (`-`). 

Note that a provided country that is not excluded can be accepted or rejected, based on the entire countries definition.

- If you **only define exclusions** (e.g. `-UK, -FR, -US`), and the provided country is *not* excluded (e.g. `NL`), then it will be **accepted**.
- If you **define both exclusions and inclusions** (e.g. `NL, EU, -FR`), a country that is not specifically included (e.g. `US`) is **rejected**. This scenario is most useful with the EU short-hand, see below. 

### European Union short-hand (v1.1+)

To immediately accept or reject all European Union Member States, add the `EU` or `-EU` short-hand respectively. At time of writing, the following country codes are considered part of the EU: 

```
['BE','BG', 'CZ', 'DK', 'DE', 'EE', 'IE', 'EL', 'ES', 'FR', 'HR', 'IT', 'CY', 'LV', 'LT', 'LU', 'HU', 'MT', 'NL', 'AT', 'PL', 'PT', 'RO', 'SI', 'SK', 'FI', 'SE', 'UK']
```

Note that if/when the United Kingdom leaves the EU (Brexit) `UK` will no longer be considered part of the EU for this particular short-hand. If it is important for your integration that `UK` is always included (or excluded), add it separately like `EU, UK` or `-EU, -UK`. 

## Processing Order

Countries are evaluated in the following order until a result is determined.

1. No countries defined => country is accepted
2. An exact inclusion match (e.g. `NL` in `BE, NL, LX`) => country is accepted
3. An exact exclusion match (e.g. `NL` in `EU, -NL, US`) => country is rejected
4. If the country is in the European Union... 
   - `EU` is defined => country is accepted
   - `-EU` is defined => country is rejected
5. If only negative matches are defined (e.g. `-NL, -BE, -LX`) => country is accepted
6. Country is rejected

## Examples

- Given an empty countries definition, all countries are accepted. (Rule 1)
- Given the countries definition `NL, BE, DE`, only NL, BE, and DE are accepted countries. (exact match, rule 2)
- Given `NL, -BE, FR`, any country except for `NL` and `FR` are considered rejected. This is technically equivalent to `NL, FR` (rule 6, because none of the rules match).
- Given `EU`, any country in the EU is accepted (rule 4).
- Given `EU, -NL`, any country in the EU is accepted (rule 4), except for the Netherlands (rule 3).
- Given `-EU, -US`, countries in the EU are rejected (rule 4), the US is rejected (rule 3), but any other country is accepted (rule 5). 
- Given `-EU, -US, CA, DK`, countries in the EU are rejected (rule 4) except for Denmark (rule 2), the US is rejected (rule 3), Canada is accepted (rule 2), and all other countries are rejected (rule 6)
- Given `EU, -US, CA, -DK`, countries in the EU are accepted (rule 4) except for Denmark (rule 3), the US is rejected (rule 3), Canada is accepted (rule 2), and all other countries outside the EU are rejected (rule 6)

## Integrating country rules in custom code (v1.1+)

To run these same exact rules in a module or other Commerce-based code, you can use the `Countries` static service. The `isAcceptedCountry` static method takes in a provided `$country` and the `$countries` definition, and returns a boolean true or false to indicate the country is to be accepted or rejected.

The method automatically normalised the country and countries to uppercase, splits the string based on a comma, and trims away any whitespace.

For example:

``` php
use modmore\Commerce\Services\Countries;

$country = 'NL';
$countries = 'EU, US, CA';
if (!Countries::isAcceptedCountry($country, $countries)) {
    throw new Exception('Country is not allowed.');
}
```

## Commerce 1.0 and before

Prior to Commerce 1.1, you can only add a country restriction to Shipping Methods, separated by a comma. The country-based shipping method also only supports the simple list.

Whitespace and upper/lower case is ignored. 

For example:

```
NL, BE, LX
```

```
US, CA, MX, VZ
```

When leaving the country availability empty, all countries are accepted. 
