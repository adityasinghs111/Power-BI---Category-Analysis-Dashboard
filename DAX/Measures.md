# Measures

## Applied Filters

```DAX
Applied Filters = 
VAR _BroadCategory =
    IF(
        COUNTROWS(ALLSELECTED('Categories'[Broad Category]))
            < COUNTROWS(ALL('Categories'[Broad Category])),
        "Broad Category"
    )

VAR _ProcmartState =
    IF(
        COUNTROWS(ALLSELECTED(InvDetails[New ProcMart State]))
            < COUNTROWS(ALL(InvDetails[New ProcMart State])),
        "ProcMart State"
    )

VAR _ReqTagType =
    IF(
        COUNTROWS(ALLSELECTED(InvDetails[Req Tag Type]))
            < COUNTROWS(ALL(InvDetails[Req Tag Type])),
        "Req Tag Type"
    )

VAR _Client =
    IF(
        COUNTROWS(ALLSELECTED(InvDetails[Calculated Client Group Name]))
            < COUNTROWS(ALL(InvDetails[Calculated Client Group Name])),
        "Client Name"
    )

VAR _AdjNoteType =
    IF(
        COUNTROWS(ALLSELECTED(InvDetails[Adj Note Type]))
            < COUNTROWS(ALL(InvDetails[Adj Note Type])),
        "Adj Note Type"
    )

VAR _KamName =
    IF(
        COUNTROWS(ALLSELECTED(InvDetails[Kam Name]))
            < COUNTROWS(ALL(InvDetails[Kam Name])),
        "Kam Name"
    )

VAR _OrderType =
    IF(
        COUNTROWS(ALLSELECTED(InvDetails[New Order Type]))
            < COUNTROWS(ALL(InvDetails[New Order Type])),
        "Order Type"
    )

VAR _BrandType =
    IF(
        COUNTROWS(ALLSELECTED(InvDetails[Calculated Brand Type]))
            < COUNTROWS(ALL(InvDetails[Calculated Brand Type])),
        "Brand Type"
    )

VAR _Routed =
    IF(
        COUNTROWS(ALLSELECTED(InvDetails[Routed]))
            < COUNTROWS(ALL(InvDetails[Routed])),
        "Routed"
    )

VAR _Top30Clients =
    IF(
        COUNTROWS(ALLSELECTED(InvDetails[Calculated Top 30 Company]))
            < COUNTROWS(ALL(InvDetails[Calculated Top 30 Company])),
        "Clients"
    )

VAR _Subcategory =
    IF(
        COUNTROWS(ALLSELECTED(Categories[Product Industry]))
            < COUNTROWS(ALL(Categories[Product Industry])),
        "Subcategory"
    )

VAR _MarginBand =
    IF(
        COUNTROWS(ALLSELECTED('Margin Brackets'[Brackets]))
            < COUNTROWS(ALL('Margin Brackets'[Brackets])),
        "Gross Margin % Band"
    )

VAR _Brands =
    IF(
        COUNTROWS(ALLSELECTED(InvDetails[Brand Name]))
            < COUNTROWS(ALL(InvDetails[Brand Name])),
        "Brand Name"
    )

RETURN
VAR _AppliedFilters =
    FILTER(
        {
            (_BroadCategory),
            (_ProcmartState),
            (_ReqTagType),
            (_Client),
            (_AdjNoteType),
            (_KamName),
            (_OrderType),
            (_BrandType),
            (_Routed),
            (_Top30Clients),
            (_Subcategory),
            (_MarginBand),
            (_Brands)
        },
        NOT ISBLANK([Value])
    )
RETURN
IF(
    ISEMPTY(_AppliedFilters),
    "No Filters Applied",
    "Filters Applied : "
        & CONCATENATEX(
            _AppliedFilters,
            [Value],
            " ◆ "
        )
)
```

## Avg SPO Unit Price

```DAX
Avg SPO Unit Price = 
DIVIDE(
    SUM(InvDetails[Spo Item Amount Before Tax]),
    SUM(InvDetails[Inv Qty])
)
```

## Gross Margin %

```DAX
Gross Margin % = 
DIVIDE(
    [Total Revenue] - [Total Cost],
    [Total Revenue],
    0
)
```

## Gross Margin % - Margin Band

```DAX
Gross Margin % - Margin Band = 
VAR SelectedBracket = SELECTEDVALUE('Margin Brackets'[Brackets])
RETURN
IF(
    ISBLANK(SelectedBracket),
    [Gross Margin %],
    VAR ClientsInContext = VALUES('InvDetails'[Calculated Client Group Name])
    VAR QualifyingClients =
        FILTER(
            ClientsInContext,
            VAR ClientMargin =
                CALCULATE(
                    [Gross Margin %] * 100,
                    REMOVEFILTERS('InvDetails'[New ProcMart State]),
                    REMOVEFILTERS('InvDetails'[Product Industry])
                )
            RETURN
                SWITCH(
                    TRUE(),
                    SelectedBracket = "Below 0%",   ClientMargin < 0,
                    SelectedBracket = "0% - 5%",    ClientMargin >= 0  && ClientMargin < 5,
                    SelectedBracket = "5% - 10%",   ClientMargin >= 5  && ClientMargin < 10,
                    SelectedBracket = "10% - 15%",  ClientMargin >= 10 && ClientMargin < 15,
                    SelectedBracket = "Above 15%",  ClientMargin >= 15,
                    FALSE()
                )
        )
    RETURN
    CALCULATE([Gross Margin %], QualifyingClients)
)
```

## Gross Margin % 2

```DAX
Gross Margin % 2 = 
DIVIDE(
    SUMX(
        InvDetails,
        InvDetails[Invoice Total (Before Tax)] -
        InvDetails[Spo Item Amount Before Tax]
    ),
    SUMX(
        InvDetails,
        InvDetails[Invoice Total (Before Tax)]
    )
)
```

## Margin Bracket Filter

```DAX
Margin Bracket Filter = 

VAR Margin =
    [Gross Margin %] * 100

VAR SelectedBracket =
    SELECTEDVALUE('Margin Brackets'[Brackets])

RETURN
SWITCH(
    TRUE(),

    SelectedBracket = "Below 0%" &&
    Margin < 0, 1,

    SelectedBracket = "0% - 5%" &&
    Margin >= 0 &&
    Margin < 5, 1,

    SelectedBracket = "5% - 10%" &&
    Margin >= 5 &&
    Margin < 10, 1,

    SelectedBracket = "10% - 15%" &&
    Margin >= 10 &&
    Margin < 15, 1,

    SelectedBracket = "Above 15%" &&
    Margin >= 15, 1,

    ISBLANK(SelectedBracket), 1,

    0
)
```

## Matrix Colour Highlight

```DAX
Matrix Colour Highlight = 
IF(
    [Purchase Count] > 3,
    "#E8D166",
    BLANK()
)
```

## Max Margin %

```DAX
Max Margin % = 
MAXX(
    InvDetails,
    DIVIDE(
        InvDetails[Invoice Total (Before Tax)] - InvDetails[Spo Item Amount Before Tax],
        InvDetails[Invoice Total (Before Tax)]
    )
)
```

## Max SPO Unit Price

```DAX
Max SPO Unit Price = 
MAXX(
    FILTER(
        InvDetails,
        InvDetails[Spo Item Amount Before Tax] > 0
    ),
    DIVIDE(
        InvDetails[Spo Item Amount Before Tax],
        InvDetails[Inv Qty]
    )
)
```

## Min Margin %

```DAX
Min Margin % = 
MINX(
    InvDetails,
    DIVIDE(
        InvDetails[Invoice Total (Before Tax)] - InvDetails[Spo Item Amount Before Tax],
        InvDetails[Invoice Total (Before Tax)]
    )
)
```

## Min SPO Unit Price

```DAX
Min SPO Unit Price = 
MINX(
    FILTER(
        'InvDetails',
        'InvDetails'[Spo Item Amount Before Tax] > 0
    ),
    DIVIDE(
        'InvDetails'[Spo Item Amount Before Tax],
        'InvDetails'[Inv Qty]
        )
)
```

## Product Specs

```DAX
Product Specs = 
VAR _product =
    SELECTEDVALUE('InvDetails'[Product Name])

VAR _spec =
    SELECTEDVALUE('InvDetails'[Product Specs])

RETURN
IF(
    NOT ISBLANK(_product) && NOT ISBLANK(_spec),
    _product & " - " & _spec,
    COALESCE(_product, _spec)
)
```

## Revenue - Margin Band

```DAX
Revenue - Margin Band = 
VAR SelectedBracket = SELECTEDVALUE('Margin Brackets'[Brackets])
RETURN
IF(
    ISBLANK(SelectedBracket),
    [Total Revenue],
    VAR ClientsInContext = VALUES('InvDetails'[Calculated Client Group Name])
    VAR QualifyingClients =
        FILTER(
            ClientsInContext,
            VAR ClientMargin =
                CALCULATE(
                    [Gross Margin %] * 100,
                    REMOVEFILTERS('InvDetails'[New ProcMart State]),
                    REMOVEFILTERS('InvDetails'[Product Industry])
                )
            RETURN
                SWITCH(
                    TRUE(),
                    SelectedBracket = "Below 0%",   ClientMargin < 0,
                    SelectedBracket = "0% - 5%",    ClientMargin >= 0  && ClientMargin < 5,
                    SelectedBracket = "5% - 10%",   ClientMargin >= 5  && ClientMargin < 10,
                    SelectedBracket = "10% - 15%",  ClientMargin >= 10 && ClientMargin < 15,
                    SelectedBracket = "Above 15%",  ClientMargin >= 15,
                    FALSE()
                )
        )
    RETURN
    CALCULATE([Total Revenue], QualifyingClients)
)
```

## Revenue (Lakh)

```DAX
Revenue (Lakh) = [Total Revenue] / 100000
```

## Revenue (Lakh) - Margin Band

```DAX
Revenue (Lakh) - Margin Band = 
VAR SelectedBracket = SELECTEDVALUE('Margin Brackets'[Brackets])
RETURN
IF(
    ISBLANK(SelectedBracket),
    [Revenue (Lakh)],
    VAR ClientsInContext = VALUES('InvDetails'[Calculated Client Group Name])
    VAR QualifyingClients =
        FILTER(
            ClientsInContext,
            VAR ClientMargin =
                CALCULATE(
                    [Gross Margin %] * 100,
                    REMOVEFILTERS('InvDetails'[New ProcMart State]),
                    REMOVEFILTERS('InvDetails'[Product Industry])
                )
            RETURN
                SWITCH(
                    TRUE(),
                    SelectedBracket = "Below 0%",   ClientMargin < 0,
                    SelectedBracket = "0% - 5%",    ClientMargin >= 0  && ClientMargin < 5,
                    SelectedBracket = "5% - 10%",   ClientMargin >= 5  && ClientMargin < 10,
                    SelectedBracket = "10% - 15%",  ClientMargin >= 10 && ClientMargin < 15,
                    SelectedBracket = "Above 15%",  ClientMargin >= 15,
                    FALSE()
                )
        )
    RETURN
    CALCULATE([Revenue (Lakh)], QualifyingClients)
)
```

## Selected Brand Name

```DAX
Selected Brand Name = 
SELECTEDVALUE('InvDetails'[Brand Name])
```

## Selected Product Name

```DAX
Selected Product Name = 
SELECTEDVALUE('InvDetails'[Product Name])
```

## Gross Margin % - Margin Band Previous Year

```DAX
Gross Margin % - Margin Band Previous Year = 
CALCULATE(
    [Gross Margin % - Margin Band],
    SAMEPERIODLASTYEAR(Calendar[Date])
)
```

## Gross Margin % Difference

```DAX
Gross Margin % Difference = 
[Gross Margin % - Margin Band] - [Gross Margin % - Margin Band Previous Year]
```

## Gross Margin % Previous Year

```DAX
Gross Margin % Previous Year = 
CALCULATE(
    [Gross Margin %],
    SAMEPERIODLASTYEAR(Calendar[Date])
)
```

## Revenue - Margin Band Previous Year

```DAX
Revenue - Margin Band Previous Year = 
CALCULATE(
    [Revenue - Margin Band],
    SAMEPERIODLASTYEAR(Calendar[Date])
)
```

## Revenue (Lakh) - Margin Band Previous Year

```DAX
Revenue (Lakh) - Margin Band Previous Year = 
CALCULATE(
    [Revenue (Lakh) - Margin Band],
    SAMEPERIODLASTYEAR(Calendar[Date])
)
```

## Revenue Difference

```DAX
Revenue Difference = 
[Revenue - Margin Band] - [Revenue - Margin Band Previous Year]
```

## Revenue Previous Year

```DAX
Revenue Previous Year = 
CALCULATE(
    [Revenue (Lakh)],
    SAMEPERIODLASTYEAR(Calendar[Date])
)
```

## Total Cost

```DAX
Total Cost = SUM('InvDetails'[Spo Item Amount Before Tax])
```

## Total Revenue

```DAX
Total Revenue = SUM('InvDetails'[Invoice Total (Before Tax)])
```
