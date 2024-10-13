# Manually add prices to influxDB

1. Open InfluxDB UI
2. Go to "Load Data" and "buckets"
3. Select the "prices" bucket
4. Use the line protocol
5. Select "enter manually"
6. Select precision "Seconds"
7. Insert data like this:
   ```
   powerPrices,tagKey=heatTariff basePriceEURMonth=10.90 1688162400
   powerPrices,tagKey=heatTariff workingPriceCtKWh=29.90 1688162400
   powerPrices,tagKey=normalTariff basePriceEURMonth=11.90 1688162400
   powerPrices,tagKey=normalTariff workingPriceCtKWh=34.90 1688162400
   ```
