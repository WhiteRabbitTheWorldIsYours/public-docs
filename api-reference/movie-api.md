# Movie API

White Rabbit exposes a GraphQL API.

Endpoints:

* production: https://api.whiterabbit.one/graphql
* test: https://staging-api.whiterabbit.one/graphql

## Get registered movies

Use `movies` subgraph to get information about the movies registered in RFR, their pricing and availability for streaming.

Example of subgraph query:

```
{
        movie {
            IMDB            
            id
            metadata {
                posterUrl
                year
                title
            }
            pricing {
                amount
                regions
                medium
                fromWindow
                toWindow
            }
            assets {
                video {
                    videoId
                }
                posterUrl
            }
        }
    }
```

Points of interest here:

`movie.IMDB` - IMDB ID of the film

`movie.id` — TMDB ID of the film. You can use it to query the film information from themoviedb.org API

`movie.metadata.posterUrl` — Film poster as provided by themoviedb.org. If rightsholder has specified different poster for his film, it can be found under `movie.assets.posterUrl`.

`movie.metadata.year` — Film release year as provided by themoviedb.org

`movie.metadata.title` — Film title in English

### Get movie prices

Prices set by rightsholder for the film and are available via `movie.pricing` subgraph. Each of the prices optionally could be bound by time, region or medium.&#x20;

Price is specified in `amount` leaf as `<EUR price> × 10^18`. So `5000000000000000000` means `5 EUR`

#### Timeframe

If the price has `fromWindow` and `toWindow` attributes specified, the price is effective only within the timeframe specified by these attributes.

#### Regions

&#x20;If the price has one or multiple regions specified, the price is effective only in these regions.

Region is specified as number in [M49](https://unstats.un.org/unsd/methodology/m49/) format. You can convert [ISO 3166-1 ](https://en.wikipedia.org/wiki/ISO\_3166-1\_alpha-2)region to M49, if you need, using libraries like [https://github.com/wooorm/un-m49](https://github.com/wooorm/un-m49). There may be many regions for the same price

#### Medium

If the price has medium specified, the price is effective only for this medium. For now White Rabbit supports only EST medium. In payments, it is encoded as 200

| Medium | Code |
| ------ | ---- |
| CINEMA | 100  |
| P2P    | 150  |
| EST    | 200  |

### Check if the movie is available

To answer the question “this film X is authorised for streaming in country Y today” you need to get “pricing” for film X (see [#get-movie-prices](movie-api.md#get-movie-prices "mention")), find the price for region Y and check if today date is between `fromWindow` and `toWindow` for the price found. If it is, then the film X is available in country Y at the price specified by resulting record. If you cannot find such a price, it means the film X is not available to be paid for via White Rabbit.

### Check if the movie has a video file for streaming

CDN video data is available at `movie.assets` subgraph.

If there is `assets.video.videoId` branch available for the movie, then this movie is available on our CDN for streaming.&#x20;

If you want to use WhiteRabbit's CDN links on your website, you need to do the following:

* provide us with the domain name of the website you want to stream the video on. We need to whitelist the domain for the CDN links to work
* then you can use the HLS playlist for the movie in your video player. Playlist is available at `https://vz-cb1fdbea-917.b-cdn.net/<videoId>/playlist.m3u8` (replace `<videoId>` with the `videoId` from the API response).&#x20;
* attach a code to request a payment for the movie using WhiteRabbit JS client ([#request-payment](payment-api.md#request-payment "mention"))

{% hint style="warning" %}
You must always use our JS client with CDN videos to request payment from the user via White Rabbit.
{% endhint %}

\




















