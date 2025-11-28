# Fabric Pipeline: pl_ingest_exchangerates

This pipeline loads daily currency exchange rates from the Frankfurter API into OneLake Raw Layer.

## Steps:
1. **Copy Activity**  
   GET https://api.frankfurter.app/{YYYY-MM-DD}?base={USD}

2. **Save JSON to OneLake**  
   /raw/exchange_rates/date=YYYY-MM-DD/base=USD/

3. **Dynamic parameters:**  
   - pDate  
   - pBaseCurrency  

4. **Schedule:**  
   Daily at 11:00 UTC

