# Movie API

White Rabbit exposes a GraphQL API.

Endpoints:

* production: https://api.whiterabbit.one/graphql
* test: https://staging-api.whiterabbit.one/graphql

## Get registered movies

Use `availableMovies` subgraph to get information about the movies registered in RFR, their pricing and availability for streaming.

Example of subgraph query:

```graphql
{
    availableMovies {
        id
        IMDB
        metadata {
            posterUrl
            title
            year
        }
        assets {
            posterUrl
        }
    }
}
```

Points of interest here:

`availableMovies.IMDB` - IMDB ID of the film

`availableMovies.id` — TMDB ID of the film. You can use it to query the film information from themoviedb.org API

`availableMovies.metadata.posterUrl` — Film poster as provided by themoviedb.org. If rightsholder has specified different poster for his film, it can be found under `movie.assets.posterUrl`.

`availableMovies.metadata.year` — Film release year as provided by themoviedb.org

`availableMovies.metadata.title` — Film title in English

`availableMovies.assets.posterUrl` — Film poster as provided by rightsholder.&#x20;

## Get movie price

Prices set by rightsholder for the film and are available via `movie.pricing` subgraph. Each of the prices could be bound by time, region or medium. When myRegion filter parameter is specified (see example below) the server will detect the region of the caller and return the matching prices, you have to use the first one in the list.

Price is specified in `amount` edge as `<EUR price> × 10^18`. So `5000000000000000000` means `5 EUR`

#### Example of the call (replace tt19816018 with IMDB Id for for the target movie):

```graphql
{ 
  movie(movieId: "tt19816018") {
      IMDB
      metadata {
          imdbId
          title
          year
          posterUrl
          genres
          runtime
      }
      assets {
          tagline
          logline
          overview
          posterUrl
          hashtags
          video {
              libraryId
              thumbnailFileName
              videoId
              captions { langCode label }
          }
          trailerVideo {
              libraryId
              thumbnailFileName
              videoId
          }
      }
      pricing(filter: { myRegion: true, medium: "EST" }) {
          amount
          currencyCode
      }
      isVisible
    }
}
```

## Use video file from WhiteRabbit's CDN

CDN video data is available at `movie.assets` subgraph.

If there is `assets.video.videoId` branch available for the movie, then this movie is available on our CDN for streaming.&#x20;

If you want to use WhiteRabbit's CDN links on your website, you need to do the following:

* provide us with the domain name of the website you want to stream the video on. We need to whitelist the domain for the CDN links to work
* then you can use the HLS playlist for the movie in your video player. Playlist is available at `https://vz-cb1fdbea-917.b-cdn.net/<videoId>/playlist.m3u8` (replace `<videoId>` with the `videoId` from the API response).&#x20;
* use a javascript code to request a payment for the movie using WhiteRabbit JS client ([#request-payment](payment-api.md#request-payment "mention"))

{% hint style="warning" %}
You must always use our JS client with CDN videos to request payment from the user via White Rabbit.
{% endhint %}

\




















