# oysttyer-multigeo
Display location of tweets and geolocate your tweets in oysttyer


## What?

This `oysttyer` extension does two main things:

* Display the geographical location of tweets
* Allow the user to define a location for the tweets

```
oysttyer> /place Rio de Janeiro, Brazil &lt;029
-- Your next tweet will be sent at -22.9112163,-43.2093781, which is near Rio de Janeiro, Brasil, South America (a city)
oysttyer> Wish my tweets could have a sunbath (testing yet another @ttytter geolocation extension) &lt;088
-- using lat/long: (-22.9112163, -43.2093781)
-- waiting 3 seconds to tweet, ^C cancels: "Wish my tweets could have a sunbath (testing yet another @ttytter geolocation extension)"
-- sending to server
c2+  Viaduto dos Fuzileiros, Praça da Bandeira, Rio de Janeiro, 20211-445, Brasil
c2> [2011-10-14 20:31:16] &lt;@+RealIvanSanchez> Wish my tweets could have a sunbath (testing yet another @ttytter geolocation extension)
```

## Usage

After downloading, enable the extension in your `.oysttyerrc` file:

```
exts=path/to/multigeo.pl
```

If you use more than one extension:

```
exts=path/to/multigeo.pl,path/to/another-extension.pl
```

The GPSD funcionality needs libnet-gpsd3-perl (run `cpan Net::GPSD3`)

The GeoIP functionality needs libgeo-ip-perl (`cpan Geo::IP`)

### Readable location of tweets

MultiGeo will automatically display the geolocation of tweets in an user-friendly, easy-to-read text string just above the tweet:

```
u5+  West Main Street, Madisonville, Madison, Texas, 77864, United States of America
u5> [2011-10-08 17:47:43] <+SeanGorman> @ajturner has gone full cowboy in honor of #ratdiver http://t.co/KPPCaYCX
```

This works only for geolocated tweets, and uses the multigeo-wide geocoder (see below). The verbosity and detail of the location will depend on the geocoder being used.

There are two kinds of geolocated tweets: those with a pair of (latitude,longitude) coordinates, and those with a Place ID. Place IDs are managed directly by the twitter API and need no name resolution (see this old announcement for context). This means that only tweets with lat-lon will be passed to the geocoder for resolution. The geolocation method will depend on the user agent software of the tweeter, and shouldn't matter much to the reader - `multigeo.pl` will display both types of geolocation (altough geocoded lat-long tends to be more verbose than Place IDs). In a nutshell, tweets sent from the web use Place IDs, whereas tweets from oysttyer (and GPS-enabled mobile clients) use coordinates, and `multigeo.pl` will display everything. If you need anything else, `/dump` the tweet or file a bug report.

### /place

By typing the command `/place [name of the place you're at]`, your tweets *from that moment on* will be geolocated there. The name of the place can be a street, city, placemark or others:

```
oysttyer> /place Rio de Janeiro, Brazil <029
-- Your next tweet will be sent at -22.9112163,-43.2093781, which is near Rio de Janeiro, Brasil, South America (a city)
oysttyer> /place NG8 1, United Kingdom  <029
-- Your next tweet will be sent at 52.950996099129,-1.19203191262383, which is near NG8 1, United Kingdom (a postcode)
oysttyer> /place Bronx, New York <022
-- Your next tweet will be sent at 40.8501002,-73.8662464, which is near Bronx, New York, United States of America (a hamlet)
oysttyer> /place Coors Field, Denver, Colorado <036
-- Your next tweet will be sent at 39.7561146965203,-104.994083810925, which is near Coors Field, Blake Street, Denver, Denver County, Colorado, 80205, United States of America (a stadium)
oysttyer> /place Statue of Liberty, Liberty Island <040
-- Your next tweet will be sent at 40.689245737137,-74.0445412130299, which is near Statue of Liberty, Hudson River Waterfront Walkway, Jersey City, Hudson County, New Jersey, 07302, United States of America (a attraction)
oysttyer> /place Hospital Clinico San Carlos, Madrid, Spain <049
-- Your next tweet will be sent at 40.4407617610234,-3.71992637213825, which is near Hospital Clínico San Carlos, Calle Profesor Martín Lagos, Chamberí, Moncloa-Aravaca, Madrid, 28008, Spain (a hospital)
oysttyer> /place Konig Frankfurter, Girona <032
-- Your next tweet will be sent at 41.9854809,2.8221996, which is near Konig Frankfurter, Gran Via de Jaume I, Girona, Generalitat de Catalunya, Catalunya, 17002, Spain (a restaurant)
```

Type `/place off` or just `/place` to turn geolocation off.

```
oysttyer> /place off <010
-- Turning geolocation off
```

This functionality uses the multigeo-wide geocoder (see below). The accuracy of your place queries will depend on the geocoder used.

Thanks to TTYtter user @xurxosanz for the idea for this functionality.


### /teleport

Maybe you want to tweet from the same place another person is tweeting from. Maybe you are at the same place as another user, or even at a event with lots of people and geo-located tweets around. Or maybe you'd wish to be there.

Just type `/teleport [tweet code]` to be there:

```
d8+  Rua Miguel Couto, Centro, Rio de Janeiro, 20070-030, Brasil
d8> [2011-10-14 16:58:02] <+Nighto> http://t.co/4rqPEKEt OH YEAH
oysttyer> /teleport d8 <013
-- Your next tweet will be sent at -22.903032,-43.17926.
oysttyer> Beam me down to Brazil, Scotty! (testing another @ttytter geolocation extension) <080
-- using lat/long: (-22.903032, -43.17926)
-- waiting 3 seconds to tweet, ^C cancels: "Beam me down to Brazil, Scotty! (testing another @ttytter geolocation extension)"
-- sending to server
e1+  Rua Miguel Couto, Centro, Rio de Janeiro, 20070-030, Brasil
e1> [2011-10-14 19:48:34] <+RealIvanSanchez> Beam me down to Brazil, Scotty! (testing another @ttytter geolocation extension)
```

Please note you won't be able to teleport to tweets that have Place IDs instead of coordinates. oysttyer cannot (as of now) post tweets with a Place ID, and so `multigeo.pl` cannot clone that ID. That's what's happenning if you see a "sorry, no geoinformation" in that tweet error message.

Thanks to oysttyer user @vehrka for the idea for this functionality.

### GPSD

GPSD is the way to go if you're tweeting from a machine which has a GPS plugged in to it. Be sure to get GPSD working first.

If everything's working, this functionality will automatically geolocate your tweets on the coordinates of your GPS receiver:

```
oysttyer> Trying out gpsd support for ttytter <035
-- No GPS fix, not using geolocation.
-- waiting 3 seconds to tweet, ^C cancels: "Trying out gpsd support for ttytter"
^C
-- not sent, cancelled by user
```

Awww, dammit. No GPS fix inside my flat. Seems I'll have to stick my hand out of the window one more time for this to work. Now, let's try again:

```
oysttyer> Trying out ttytter+gpsd geolocation (again)  <043
-- using lat/long: (40.7379316893733, -3.44560555824903)
-- waiting 3 seconds to tweet, ^C cancels: "Trying out ttytter+gpsd geolocation (again)"
-- sending to server
```

By default, GPSD functionality is not active. You need to set the option `extpref_multigeo_gpsd=true` in your `.oysttyerrc` file. Once oysttyer is running, you can issue the commands `/gpsd on` and `/gpsd off` to enable/disable GPSD functionality. Typing the `/gpsd` command alone will query the current GPS status (offline / no fix / fix and coordinates).

Please note that `/place` and `/teleport` implies `/gpsd off`. Conversely, `/gpsd on` will override any previous `/place` or `/teleport` command.

Be warned that GPS functionality has not been throughoutly tested. Please report bugs and patches.

### GeoIP

GeoIP is the poorman's GPS. Based on your current IP and a IP-to-city database, it is able to set your general location. Don't expect accurate geolocation with this method. You might even end up in the wrong city if ISPs do a lazy job.

```
oysttyer> Trying out #GeoIP extension for @ttytter <040
-- GeoIP extension will try to locate your public IP address now
-- GeoIP: 83.50.223.514 is near Valencia, Comunidad Valenciana, Spain
-- using lat/long: (39.4667, -0.3667)
-- waiting 3 seconds to tweet, ^C cancels: "Trying out #GeoIP extension for @ttytter"
-- sending to server
c2+  Císcar - Comte d'Altea, Calle de Císcar, Eixample, Valencia, València/Valencia, Comunitat Valenciana, 46006, Spain
c2> [2011-10-26  0:40:54] <+RealIvanSanchez> Trying out #GeoIP extension for @ttytter
```

GeoIP location will only happen once, just before your first tweet, and only if you've set `$extpref_multigeo_geoip=true` in your .ttytterrc file. If coordinates are already set (e.g. GPSD got a fix, or you've run `/place` before your first tweet), GeoIP location will not happen. It is quite fair to assume that if coordinates are already set, they're better than the ones GeoIP can provide.

Be warned that GeoIP functionality is not usable at this moment. If you know how to fetch your public IP from Perl, or how to make STUN work, please do let me know. In the meantime, just use `/place [your city]`

### Shared geocoder

The `/place` and readable locations share the geocoder. A geocoder is a external service that translates a placename into latitude-longitude coordinates and viceversa. Right now there are three geocoders available:

* The OpenStreetMap Nominatim geocoder queries que OpenStreetMap database, containing detailed information about most streets, and businesses if you're lucky. Please do check out http://www.openstreetmap.org to check if places near you are in the database (add them if they're not in there! It's a wiki!), and check out http://wiki.openstreetmap.org/wiki/Nominatim or else I will die in the hands of angry lawyers because you didn't read Nominatim's terms of use.

* The GeoNames geocoder queries the much simpler http://www.geonames.org database, which only contains data at city level (i.e. the most detailed information you can get from GeoNames is the name of a city).

* The Twitter API geocoder queries two API 1.1 endpoints (for direct and reverse geocoding). This geocoder allows you to fetch the very same placenames that other pure twitter clients show you. I'm guessing this is the "official" way to do geostuff according to the Twitter guys. But, be warned this geocoder can use up your API rate limit more quickly.

Finally, the null geocoder does absolutely nothing. Use this if you don't care about seeing the location of other tweets, and if you won't use `/place`.

By default, MultiGeo will use OpenStreetMap's Nominatim. In order to manually specify a geocoder, add one of those to your .ttytterrc file:

```
    extpref_multigeo_geocoder=nominatim
    extpref_multigeo_geocoder=geonames
    extpref_multigeo_geocoder=twitter
    extpref_multigeo_geocoder=none
```

And before anyone asks: there will not be a Google geocoder, due to Google's terms of use. Ideas about other geocoders will be appreciated.

Note that, right now, oysttyer only has the ability to post tweets with coordinates, and not place IDs. A Place ID geocoder might be feasible, if there is interest from the oysttyer user community.



## Legalese

---

  "THE BEER-WARE LICENSE":
<ivan@sanchezortega.es> wrote this file. As long as you retain this notice you
can do whatever you want with this stuff. If we meet some day, and you think
this stuff is worth it, you can buy me a beer in return.

---