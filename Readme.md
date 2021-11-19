# UnOfficial Clone Of [Torrent-AIO-Bot](https://github.com/patheticGeek/torrent-aio-bot)
This is an unofficial clone of **[Torrent-AIO-Bot](https://github.com/patheticGeek/torrent-aio-bot)**.


## Deploy 👀

If you like to make your own version of [this mod](https://github.com/Itz-fork/Torrent-Aio-Bot-Duplicate), just click on "Deploy to Heroku" button 👇:

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/Itz-fork/Torrent-Aio-Bot-Duplicate)


> Note ⚠️ : The search feature is currently unmaintained due to the inconsistency between proxy sites and their uptimes
>


## Configs 📓

#### Fill Before deploy,

- `SITE` - Your Heroku app url
- `SEARCH_SITE` - Your Heroku app url (You can also set this to "https://torrent-aio-bot.herokuapp.com/". [Not my site tho])
- `TELEGRAM_TOKEN` - Your telegram bot token (Get it from [@BotFather](https://t.me/BotFather))
- `GDRIVE_PARENT_FOLDER` - Your Google Folder Id (Team drive id supported)

#### Fill after deploy

- `CLIENT_ID` ⭐ - Your Google Drive Drive API Client ID (Defaults to: `202264815644.apps.googleusercontent.com` [rcone](https://rclone.org/)'s client id)
- `CLIENT_SECRET` ⭐ - Your Google Drive Drive API Client Secret (Defaults to: `X4Z3ca8xfWDb1Voo-F9a7ZxJ` [rcone](https://rclone.org/)'s client secret)
- `TOKEN` - Get this token from  https://<appname>.herokuapp.com/drivehelp (<appname> is your heroku app name)
- `AUTH_CODE` - While Obtaining the "TOKEN" your website (heroku app) will ask you to login to your account. After a successful login you'll get this

⭐ - If you don't have those just don't fill them **but** if you have remember to use your own Google Dirve credentials


## TODO after deploy

### To disable website

If you only want telegram bot to be workinh set value of DISABLE_WEB env var to true.

### To get torrent download working:

Set a variable with key "SITE" and value is the link of your site. eg. "https://\<project name>.herokuapp.com". This is important to keep bot alive or server will stop after 30 min of inactivity.

### To start a torrent bot:

Set a enviorment variable with key "TELEGRAM_TOKEN" and token of your bot as value. [How to get token](https://core.telegram.org/bots/#creating-a-new-bot)
To set a enviorment variable go to heroku dashboard open the app then go to Settings > Config vars > Reveal Config vars.

### To get search working:

The library used for web scrapping the torrent sites requires a custom buildpack on heroku. By default the search will happen on your deployment and you will need to configure the buildpack as described below. But if you don't want to do that you can specify and env SEARCH_SITE and set value to https://torrent-aio-bot.herokuapp.com/ . The frwd slash at end is necessary. This will make all the searches go thru my deployment and you don't need to configure buildpack.

Go to the build packs section in settings and click add buildpack and enter "https://github.com/jontewks/puppeteer-heroku-buildpack.git" as buildpack url then click save changes. And then do a dummy git commit so that heroku will buid it using the buildpack this time. Then set the SEARCH_SITE env to same value as SITE.

### To get gdrive upload:

1. Go to https://developers.google.com/drive/api/v3/quickstart/nodejs and click on Enable the Drive API
![](https://user-images.githubusercontent.com/77770753/114364329-e982df80-9b96-11eb-93b7-c88aed24ef25.png)

   copy client id and set an enviorment variable in heroku with name CLIENT_ID then copy client secret and set another env named CLIENT_SECRET.
2. Goto https://\<project name>.herokuapp.com/drivehelp and paste your client id and secret and click "Get auth code", it will redirect you to login and you'll get a auth code after login paste that auth code in the auth code feild and click "Generate token" it'll give you a token. now set these as env variable CLIENT_ID, CLIENT_SECRET, AUTH_CODE and TOKEN.
3. By default files are uploaded in the root of drive if you dont want to upload in root folder make a folder copy its id and set a env var GDRIVE_PARENT_FOLDER and value id of desired folder. The folder id will be the last part of the url such as in url "https://drive.google.com/drive/folders/1rpk7tGWs_lv_kZ_W4EPaKj8brfFVLOH-" the folder id is "1rpk7tGWs_lv_kZ_W4EPaKj8brfFVLOH-".
4. If you want team drive support open your teamdrive and copy the folder id from url eg. https://drive.google.com/drive/u/0/folders/0ABZHZpfYfdVCUk9PVA this is link of a team drive copy the last part "0ABZHZpfYfdVCUk9PVA" this will be your GDRIVE_PARENT_FOLDER. If you want them in a folder in teamdrive open the folder and use that folder's id instead.
5. You're good to go. The gdrive status will be shown in gdrive.txt file when you click Open on the website downloads page. Bot wil automatically send you drive link when its uploaded.

> Use this torrent for testing or when downloading to setup drive it is well seeded and downloads in ~10s
>
> magnet:?xt=urn:btih:dd8255ecdc7ca55fb0bbf81323d87062db1f6d1c&dn=Big+Buck+Bunny

## Changing the sites used for searching

To change the pirate bay site, visit the site you would like to use search something there, copy the url eg. https://thepiratebay.org/search/whatisearched and replace the search with {term} so the url looks like https://thepiratebay.org/search/{term} ans set this to env var PIRATEBAY_SITE

Same, if you want to change the limetorrents site visit the site you want to use and search for something, then replace the thing you searched for with {term} so final url looks like https://limetorrents.at/search?search={term} and set this value to env var LIMETORRENT_SITE

Simillarly the enviorment variable for 1337x is O337X_SITE

## API Endpoints

Prefix: https://\<project name>.herokuapp.com/api/v1

### For downloading:

| Endpoint          |    Params    |                                                                Return |
| :---------------- | :----------: | --------------------------------------------------------------------: |
| /torrent/download | link: string | { error: bool, link: string, infohash: string errorMessage?: string } |
| /torrent/list     |     none     |                    {error: bool, torrents: [ torrent, torrent, ... ]} |
| /torrent/remove   | link: string |                                { error: bool, errorMessage?: string } |
| /torrent/status   | link: string |                 {error: bool, status: torrent, errorMessage?: string} |

link is magnet uri of the torrent

```
torrent:  {
  magnetURI: string,
  speed: string,
  downloaded: string,
  total: string,
  progress: number,
  timeRemaining: number,
  redableTimeRemaining: string,
  downloadLink: string,
  status: string,
  done: bool
}
```

### For searching:

| Endpoint        |            Params            |                                                          Return |
| :-------------- | :--------------------------: | --------------------------------------------------------------: |
| /search/{site}  | query: string, site?: string | {error: bool, results: [ result, ... ], totalResults: number, } |
| /details/{site} |        query: string         |                                         {error: bool, torrent } |

query is what you want to search for or the link of the torrent page
site is the link to homepage of proxy to use must have a trailing '/'

```
result: {
  name: string,
  link: string,
  seeds: number,
  details: string
}

torrent: {
  title: string,
  info: string,
  downloadLink: string,
  details: [ { infoTitle: string, infoText: string } ]
}
```

sites available: piratebay, 1337x, limetorrent

## Credits & Thanks To,

- **[PathetikGeek](https://github.com/patheticGeek/torrent-aio-bot)** - For The Whole Repo!
- **[rony-alt-ac](https://github.com/rony-alt-ac)** - For Search Fix!
