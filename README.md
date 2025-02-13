# Shteff - a Discord Music Bot

Shteff is a Discord bot that allows users to play music in a voice channel and manage playlist straight from Discord using various commands. The bot uses the Spotify API to gather information about songs as well as the Discord.py library to interact with the Discord API, the yt-dlp library to stream audio from YouTube and the lyricsgenius library to get the lyrics of songs. The database is handled by MySQL.

<!--

## Getting Started

Invite Shteff using this [link](https://example.com), select a server and approve all permissions. Enjoy!

-->

## Self Hosting

### Installing requirements

Shteff requires `python 3.7` or greater.

To use this bot, you'll need to install all of the requirements from requirements.txt. You can use `pip` in your terminal to install requirements.

```
pip install library-name
```

You will also need ffmpeg.

### Setting up FFmpeg

#### Step 1: Download FFmpeg

- You can get ffmpeg [here](https://www.gyan.dev/ffmpeg/builds/).
- Download the latest git master branch build (ending in `.7z`).
- Extract the downloaded zip to `C:\ffmpeg`.

#### Step 2: Add FFmpeg to PATH

- In your Windows search bar, start typing "Edit system environment variables" and open it.
- In the bottom right corner click on "Environment Variables...".
- In the "System variables" menu double click on "Path".
- Click on "New" and enter `C:\ffmpeg\bin`.
- Click "OK" on all windows.

### Setting up a Discord bot client

#### Step 1: Create a Discord bot

- Follow the step by step guide in the [Discord.py documentation](https://discordpy.readthedocs.io/en/stable/discord.html).
- When selecting scopes, the following are needed:
    - bot
    - applications.commands
- When selecting bot permissions, the following are needed:
    - Read Messages/View Channels
    - Send Messages
    - Manage Messages
    - Connect
    - Speak

#### Step 2: Set Privileged Gateway Intents

- Go to the `Bot` tab in your application.
- Scroll down until you see the `Priviliged Gateway Intents` section.
- Enabe `SERVER MEMBERS INTENT`.

### Creating a database

#### Step 1: Download MySQL

- Follow this [guide](https://www.w3schools.com/mysql/mysql_install_windows.asp) to install MySQL.

#### Step 2: Setup a database

- When you've successfully set up your MySQL Workbench, you should run the following queries in your Workbench:
```
CREATE DATABASE Shteff;
USE Shteff;

CREATE TABLE Guilds(
guild_id BIGINT PRIMARY KEY,
channel_id BIGINT NOT NULL
);

CREATE TABLE ServerPlaylists(
guild_id BIGINT NOT NULL,
playlist_name VARCHAR(30) NOT NULL,
PRIMARY KEY(guild_id, playlist_name)
);

CREATE TABLE PersonalPlaylists(
user_id BIGINT NOT NULL,
playlist_name VARCHAR(30) NOT NULL,
PRIMARY KEY(user_id, playlist_name)
);

Create Table Songs(
song_id INT NOT NULL PRIMARY KEY auto_increment,
song_name VARCHAR(150) NOT NULL,
author_name VARCHAR(150) NOT NULL,
song_link VARCHAR(2000) NOT NULL
);
```
- After running this script, you should have Shteff's database set up! Make sure your MySQL server is running while Shteff is operational, otherwise several functionalities may be unavailable.

### Setting up a Spotify app

If you want to do this on your own, you will need to note down your `refresh_token` and the `base64_encoded_string`.

A step by step guide is provided below, but you can always use the Spotify for Developers Web API [documentation](https://developer.spotify.com/documentation/web-api/tutorials/getting-started) for more information or clarification.

#### Step 1: Generate your Spotify `client_id` and `client_secret`

- Go to [Spotify developers dashboard](https://developer.spotify.com/dashboard).
- Then select or create your app.
- Note down your Client ID and Client Secret in a convenient location to use in the next steps.

#### Step 2: Add `Redirect URIs` to your Spotify app

- Open settings for your app.
- Add `https://example.com` as your `Redirect URIs`
- Click on save.

#### Step 3: Create URI for access code

- In the URL below, replace `$CLIENT_ID`, `$SCOPE` and `$REDIRECT_URI` with the information you noted in Step 1. Make sure the `$REDIRECT_URI` is [URL encoded](https://meyerweb.com/eric/tools/dencoder/).

```
https://accounts.spotify.com/authorize?response_type=code&client_id=$CLIENT_ID&scope=$SCOPE&redirect_uri=$REDIRECT_URI
```

#### Step 4: Get acces code from the redirect URI
- You will be redirected to your redirect URI.
- In the address bar you will find a huge URL string similar to the one below. In place of `$ACCESSCODE` there will be a long string of characters. Note down that string for the next step.

```
https://example.com/?code=$ACCESSCODE
```

#### Step 5: Get the refresh token

- Type the following CURL command in your terminal and replaces all the variables with the information you noted in Step 1 and Step 4: `$CILENT_ID`, `$CLIENT_SECRET`, `$CODE`, and `$REDIRECT_URI`.

```
curl -d client_id=$CLIENT_ID -d client_secret=$CLIENT_SECRET -d grant_type=authorization_code -d code=$CODE -d redirect_uri=$REDIRECT_URI https://accounts.spotify.com/api/token
```
- The resulting JSON string will look something like this. Note down the `refresh_token`. This token will last for a very long time and can be used to generate a fresh `access_token` whenever it is needed.

```
{
  "access_token": "ACCESS_TOKEN",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "REFRESH_TOKEN",
  "scope": "playlist-modify-private"
}
```

#### Step 6:

- Go to the [Base64 Encoder](https://www.base64encode.org/) and set the Destination character set to `ISO-8859-1`.
- In the encode field enter your `$CLIENT_ID` and `$CLIENT_SECRET` and note down the result.

```
$CLIENT_ID:$CLIENT_SECRET
```
- Note down the Base64 encoded string as you will need it later.

### Setting up the Genius API

First you'll need to sign up for a (free) account that authorizes access to the [Genius API](http://genius.com/api-clients). After signing up/logging in to your account, head to the API section on Genius and [create a new API client](https://genius.com/api-clients/new). After creating your client, you can generate an access token to use with the library. Genius provides two kinds of tokens: `client access token` and `user token`. You will need the first one.

Genius doesn't require user authentication and you can easily get your token by visiting the [API Clients](https://genius.com/api-clients) page and click on "Generate Access Token". This will give you an access token, and now you're good to go.

### Setting up the `.env` file

After all that, you will need to make a file from which Shteff can access your new accounts.

In the `src` directory create a `.env`  and fill out the the needed info:

```
DISCORD_TOKEN='your-bot-token-here'

HOST_NAME='host-name-of-your-database-here'
USER_NAME='database-username'
USER_PASSWORD='database-password'
DB_NAME='database-name'
PORT_NUMBER=database-port-number

REFRESH_TOKEN='your-spotify-refresh-token'
BASE_64='your-spotify-base64-string'

GENIUS_CLIENT_ACCESS_TOKEN='your-genius-api-token'
```

Finally, run the bot by running the `main.py` file.

The bot should now be online and ready to use!

# Usage

You can interact with Shteff in two ways:
- Interactive buttons
- Slash commands

You can add songs using one of four commands: `/play`, `/file-play`, `/playlist` or `/server-playlist`. When the first song is added to the queue, a new session is started. The song queue only persists while a session is active. A session is closed by using `/clear`, `/dc` or the corresponding buttons.

If you like a song, you can save it to an existing playlist or create a new one. Playlists do not depend on session as they are stored in a database. You do not need to log in to use playlists and Shteff does not collect any user data except for the Discord User ID which is public and can be accessed by anyone. Your playlists are only accessible when logged into Discord. Server playlists are only available in the specific Discord server.

## The Buttons

The button block is located under the command message and looks like this. You can find what every command means in the list of supported commands below.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://user-images.githubusercontent.com/104315710/232313431-ef71cdfa-ab6d-4f54-ae63-e258c2cdeb04.png">
  <source media="(prefers-color-scheme: light)" srcset="https://user-images.githubusercontent.com/104315710/232313459-c617f7c0-a994-42f2-9885-45c0b2957667.png">
  <img alt="Screenshot of button block" src="https://user-images.githubusercontent.com/104315710/232313431-ef71cdfa-ab6d-4f54-ae63-e258c2cdeb04.png">
</picture>

| Shuffle | Back | Pause | Skip | Cycle loop |
| :---: | :---: | :---: | :---: | :---: |
| **Clear** | **Disconnect** | **Toggle lyrics** | **Add to user** | **Add to server playlist** |

## The Commands

Envoke a slash command by typing `/` into the Message field of any channel in your Discord server. You can use `Tab` to autocomplete the names of commands, playlists and saved songs. You will understand it the moment you see it.

Optional parameter are marked as `parameter_name`*.

Only server Administrators and users with a role named `dj` can execute commands marked with `command_name`**.

| Command | Description | Parameters | Parameter descriptions |
| --- | --- | --- | --- |
| `/help` | Shows you a help message similar to this table |||
| `/ping` | Pings Shteff and displays the latency. |||
| `/join` | Makes the bot join to your voice channel. |||
|`/play` | The command you will be using most often. Connects the bot to your voice channel and starts playing whatever you asked it to. Shteff currently supports directly searching for songs by name, any Spotify link, and youtube.com links. | `song`, `place`* | `song` is the search parameter by which Shteff will find the song(s) you are looking for. `place` will insert the song(s) at that position in the queue. You can see the queue in the command message located in the shteffs-disco text channel. `place` has to be in queue and greater than 0. |
| `/file-play` | You can add your own files to add to your queue. The supported file formats are: `mp4` , `mp3`, `flac`, `m4a`, `wav`, `wma`, `aac` | `file`, `place`* | Insert your file into the `file` parameter. `place` works the same as in `/play` |
| `/skip` | Skips the currently playing song. If a single song is looped, the next song is played and loop is set to loop the entire queue. |||
| `/loop` | Cycles the loop state in the following order: no looping, looping the entire queue, looping a single song. |||
| `/clear` | Empties the queue of songs. |||
| `/dc` | Disconnects the bot from your voice channels and resets all bot states. |||
| `/back` | Goes to the previous song. If a single song is looped, the next song is played and loop is set to loop the entire queue. |||
| `/lyrics` | Shows the lyrics of the currently playing song below the command message. |||
| `/shuffle` | Toggles the shuffling of the list. Songs skipped while shuffled will not appear in the queue after unshuffling. |||
| `/swap` | Swaps the songs with the specified places in the queue. | `first`, `second` | Places of the songs you want to swap places. `first` does not have to be less than `second`. Both parameters have to be greater than 0. |
| `/pause` | Toggles the pausing of the player. |||
| `/remove` | Removes the song in the specified place from the queue. | `place` | `place` is the position of the song you want removed from the queue. The parameter has to be greater than 0. |
| `/goto` | Moves the player to the specified position in the queue. All the skipped songs will still appear in the history. | `place` | `place` is the position of the song you want to go to. The parameter has to be greater than 0. |
| `/create` | Creates a new personal playlist which only you will be able to access. | `playlist` | The parameter describes the name of your new playlist. |
| `/server-create`** | Creates a new server playlist which everyone on the server will be able to access. | `playlist` | The parameter describes the name of your new playlist. |
| `/delete` | Deletes the playlist from your list of playlists. | `playlist` | The parameter describes the name of the playlist being deleted. |
| `/server-delete`** | Deletes the playlist from the server's list of playlists. | `playlist` | The parameter describes the name of the playlist being deleted. |
| `/add` | Adds a song to the specified playlist. | `playlist`, `song`* | The parameter describes the name of the playlist where the song(s) will be added to. If `song` is not given, the currently playing song is added to the playlist. `song` supports all formats as in the `/play` command. |
| `/server-add`** | Works the same as `/add` but for server playlists. | `playlist`, `song`* | The parameters work the same as in `/add`. |
| `/obliterate` | Removes the specified song from a personal playlist. | `playlist`, `song` | Names of the playlist and the song wanted. |
| `/server-obliterate`** | Removes the specified song from a server playlist. | `playlist`, `song` | Names of the playlist and the song wanted. |
| `/catalogue` | Lists out all your personal playlists. |||
| `/server-catalogue` | Lists out all playlists in a server. |||
| `/manifest` | Lists out all songs on a personal playlist. | `playlist` | The name of the playlist. |
| `/server-manifest` | Lists out all songs on a server playlist. | `playlist` | The name of the playlist. |
| `/playlist` | Adds your personal playlist to the queue. | `playlist`, `song`\*, `place`\* | `playlist` is the name of the playlist. If `song` is not given, the entire playlist will be added to the queue. If `song` is given, only the selected song will be added to the queue. `place` works the same as in `/play`. |
| `/server-playlist` | Works the same as `/playlist` but for server playlists. | `playlist`, `song`\*, `place`\* | The parameters work the same as in `/playlist`. |
| `/reset` | Restarts Shteff in a server. This commands is useful if you discovered a breaking bug and don't want to restart the entire bot. After running this command, a new `Player` object is created for the server in which the command was used. This is a debug command and should not be regularly used. |||
| `/refresh` | If something broke, you can refresh the command message using this command. |||

## The Command Message

The command message should look something like this. It will be located in the `shteffs-disco` text channel which Shteff will create on his own.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://user-images.githubusercontent.com/104315710/232313619-ee911e19-d017-4f76-b4ba-4de1ca38a165.png">
  <source media="(prefers-color-scheme: light)" srcset="https://user-images.githubusercontent.com/104315710/232313581-b027e100-a3c3-4175-9774-dd35ddac3e66.png">
  <img alt="Screenshot of command message" src="https://user-images.githubusercontent.com/104315710/232313619-ee911e19-d017-4f76-b4ba-4de1ca38a165.png">
</picture>

The command message consists of three parts:
1. The queue display
    - Songs with a negative number have already been played, songs with a positive number are waiting to be played.
    - The song with number 0 is currently being played.
    - Songs are played in order, from top to bottom.
    - The user can modify the order using commands and buttons.
    - When a command is taking a `place` argument, it is referring to the number next to a queued song. Commands only work with positive values of `place`.
2. The embed
     - The embed displays information about the currently playing song.
     - All information about the song displayed is taken from Spotify. The message may display incorrect information if a song is not available on Spotify.
     - The track links link to the song on other platforms (for example YouTube).
     - The author links link to the author profiles on Spotify.
     - In rare cases, the song being played may not be the same song as the one being displayed in the command message.
3. The button block
     - The button block is already described [here](README.md#the-buttons).

# Notice

This project is not actively maintained, and no loger works with the Discord and Spotify APIs.

# License

This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE.md) file for details.
