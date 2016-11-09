[![Build Status](https://travis-ci.org/MewsSystems/eet.svg?branch=master)](https://travis-ci.org/MewsSystems/eet)

# EET
EET stands for Elektronická Evidence Tržeb, which is Czech version of Fiscal Printers.
It's an online API provided by the Ministry of Finance in a form of SOAP Web Service.

## Why to use this client?
While implemented in Java, we did not find any way how to consume the EET web service using WCF as we did not find any WCF configuration that would produce SOAP message signature that would be accepted by the EET WS.
Also the autogenerated XML mapping (based on provided WSDL and XML schema) is a bit cumbersome, especially hard to understand by foreigners as it is full of Czech abbreviations.

## Key features
- no WCF
- no Czech naming in interfaces
- early data validation

## Known issues
- As the communication is done fully via HTTPS, we postponed the implementation of response signature verification. It's a potential security risk that will be addressed in upcoming releases.

## Usage
We tend to use immutable DTOs wherever possible, especially to ensure data validity.
We want the library to throw an error as soon as possible, i.e. when constructing corresponding data structures.
That is why we even introduce wrappers for simple datatypes.
Various usages are demonstrated in our test cases.

### Simplest usage example
```csharp
var certificate = new Certificate(
    password: "certificatePassword",
    data: certificateContentsByteArray
);

var record = new RevenueRecord(
    identification: new Identification(
        taxPayerIdentifier: new TaxIdentifier("CZ1234567890"),
        registryIdentifier: new RegistryIdentifier("01"),
        premisesIdentifier: new PremisesIdentifier(1),
        certificate: certificate
    ),
    revenue: new Revenue(
        gross: new CurrencyValue(1234.00m)
    ),
    billNumber: new BillNumber("2016-321")
);

var client = new EetClient(certificate);
var response = client.SendRevenue(record);
if (response.isSuccess())
{
    var fiscalCode = response.Success.FiscalCode;
}
```

# Authors
Development: [@jirihelmich](https://github.com/jirihelmich)

Code review: [@siroky](https://github.com/siroky), [@onashackem](https://github.com/onashackem)

The time to implement this was kindly provided by [Mews Systems](http://mewssystems.com).
