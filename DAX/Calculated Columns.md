# Calculated Columns

## Calculated Brand Type

```DAX
Calculated Brand Type = 
IF(
    'InvDetails'[Brand Name] = "Generic",
    "Non Branded", "Branded"
)
```


## Calculated Client Group Name

```DAX
Calculated Client Group Name = 
VAR InvoiceNo = TRIM('InvDetails'[invoiceNumber])
VAR BuyerName = 'InvDetails'[Buyer Company Name]

RETURN
SWITCH(
    TRUE(),

    -- Colgate override
    InvoiceNo IN {"21276001269", "20276046032", "21276001269.0", "20276046032.0"},
        "COLGATE",

    -- Reliance rule
    NOT ISBLANK(BuyerName) &&
    CONTAINSSTRING(UPPER(BuyerName), "RELIANCE"),
        "RELIANCE",

    -- New Buyer to Client Group Mapping
    BuyerName = "Sterlite Electric Limited", "STERLITE POWER",
    BuyerName = "Blink Commerce Private Limited", "BLINKIT",
    BuyerName = "Graziano Trasmissioni India Private Limited", "DANA",
    BuyerName = "Tata Consumer Products Limited", "TATA",
         CONTAINSSTRING(BuyerName, "Apollo Micro"), "APOLLO MICRO",
    BuyerName = "Siemens Ltd", "SIEMENS",
    BuyerName = "Amber Enterprises India Limited", "AMBER",
    BuyerName = "Instakart Services Private Limited", "FLIPKART",
    BuyerName = "Apollo Tyres Limited", "APOLLO",
     UPPER(BuyerName) = "ARAV STRATEGIES" && UPPER(InvDetails[Product Industry]) IN { "BIO MASS", "BIO-MASS", "BIOFUEL" }, "NTPC",
   

    -- Default (fallback to original Client Group Name)
    'InvDetails'[clientGroupName]
)
```

## Calculated MonthYearSort

```DAX
Calculated MonthYearSort = 
YEAR('InvDetails'[invoiceDate]) * 100 +
MONTH('InvDetails'[invoiceDate])
```

## Calculated Procmart Region

```DAX
Calculated Procmart Region = 
VAR State = TRIM(UPPER('InvDetails'[New ProcMart State]))

RETURN
SWITCH(
    TRUE(),

    -- EAST
    State IN {
        "BHADRAK","DELHI-A","DELHI-B","DELHI-C",
        "BHUBANESWAR","GUWAHATI","KOLKATA","PATNA","RAIPUR"
    }, "EAST",

    -- WEST
    State = "GOA", "WEST",

    -- GUJARAT
    State IN {"AHMEDABAD","GANDHIDHAM","SURAT","VADODRA"}, "GUJARAT",

    -- MAHARASHTRA
    State IN {"NAGPUR","PUNE-A","PUNE-B","PUNE-C","PUNE-D","PUNE-E"}, "MAHARASHTRA",

    -- NOIDA CS
    State IN {"NOIDA CS"}, "NOIDA CS",

    -- NOIDA HQ
    State IN {"NOIDA HO"}, "NOIDA HO",
  -- NOIDA PM PHYGITAL
    State IN {"PM PHYGITAL"}, "NOIDA PM PHYGITAL",
 
 -- NOIDA HO FABRICATION
    State IN {"NOIDA HO FABRICATION"}, "NOIDA HO FABRICATION",

    -- NOIDA GEM
    State IN {"NOIDA GEM"}, "NOIDA GEM",

    -- NOIDA PAAS
    State IN {"NOIDA PAAS"}, "NOIDA PAAS",

    
    -- NOIDA HO BIO-FUEL
    State IN {"NOIDA HO BIO-FUEL"}, "NOIDA HO BIO-FUEL",


    -- NOIDA PACKAGING
    State IN {"NOIDA PACKAGING","NOIDA PACKAGING - E COM"}, "NOIDA PACKAGING",

    -- NORTH
    State IN {"BADDI","HARIDWAR","INDORE","UDAIPUR","PANCHKULA-A","PANCHKULA-B","RUDRAPUR"}, "NORTH",


    -- SOUTH
    State IN {"CHENNAI","HYDERABAD","KOCHI","TRICHY","BENGALURU"}, "SOUTH",

    -- Default
    "OTHER"
)
```

## Calculated Top 30 Company

```DAX
Calculated Top 30 Company = 
IF(
    'InvDetails'[Calculated Client Group Name] IN {
        "AB INBEV",
        "ALOK INDUSTRIES",
        "AMBER",
        "APOLLO",
        "ATHER",
        "AWL",
        "BRITANNIA",
        "CADBURY",
        "CARGILL",
        "CEAT",
        "COCA COLA",
        "COLGATE",
        "CROMPTON",
        "DANA",
        "DIAGEO",
        "FAURECIA",
        "GRUPO BIMBO",
        "HARMAN",
        "HUL",
        "HZL",
        "ITC",
        "KIRLOSKAR",
        "P&G",
        "PEPSICO",
        "PIRAMAL GLASS",
        "ROCKMAN INDUSTRIES",
        "SIEMENS",
        "TENNECO",
        "VIATRIS",
        "WHIRLPOOL"
    },
    "Top 30",
    "Others"
)
```
