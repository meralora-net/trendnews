## Posting data to Influx

    import influxdb_client
    from influxdb_client.client.write_api import SYNCHRONOUS
    import datetime

    bucket = "Test Bucket"
    org = "Trends & Prices"
    # token is individual for user
    token = ""

    # when connected over VPN:
    url="http://trendy-host:8086"

    # instantiate the client
    client = influxdb_client.InfluxDBClient(
    url=url,
    token=token,
    org=org
    )

    # use the write_api method to configure the writer object
    write_api = client.write_api(write_options=SYNCHRONOUS)

    #Either
    # append point objects to write to InfluxDB (or write them in list)
    datapoints = []
    datapoints.append(
        influxdb_client.Point("PriceScraperYahoo")
        .tag("Stock", "NVD")
        .field("Price", 115.22)
        .time(int(datetime.strptime('2021-11-07','%Y-%m-%d')))
    )
    datapoints.append(influxdb_client.Point("TrendScraper").tag("stock", "MSFT").field("probability", 0.45)) #<- no timestamp -> database will add current
    datapoints.append(influxdb_client.Point("NewsScraper").tag("News", "YahooFinance").field("Complete", "Headline.... - Complete Article")) #<- no timestamp -> database will add current

    # OR write them in a dict-style 
    datapoints = ([
    {
    "measurement": "MSFT",
    "tags": {"stock": "MSFT"},
    "fields": {
    "Open": 62.79,
    "High": 63.38,
    "Low": 62.13,
    },
    "time": int(datetime.strptime('2021-11-07','%Y-%m-%d').timestamp())
    },
    ],write_option=ASYNCHRONOUS)

    # the write method requires three parameters: bucket, org, and record
    write_api.write(bucket=bucket, org=org, record=datapoints, write_precision='s')
