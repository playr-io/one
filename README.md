# Playr::One

This is the documentation for the Playr Xbox One API, if you need support or have any feedback, please jump on [![Gitter chat](https://badges.gitter.im/playr-io/one.png)](https://gitter.im/playr-io/one) and join the discussion.  
If you have found a bug or you just don't feel like joining the chat, please create an [issue](https://github.com/playr-io/one/issues), and I will deal with it as soon as I can.

The API is currently in closed Beta, if you would like access get in touch on the channel.

# Caveats

At launch all restrictions will be lifted, but for the moment they are in place so that:
- You can start interacting with the API quickly.
- There is no risk of exposing any private information.
- No destructive actions can be carried out, accidentally or maliciously.

**All restrictions will be lifted upon launch.**

The primary restriction is that all routes are currently hardcoded to authenticate as my own Xbox Live account for the duration of the Beta.  It is possible to access other users public data, but this will occur from the context of my account, for example, you will not be able to access the `Follower` list for any other users, just mine.

The next restriction is that I've disabled most of the actions around `Friend` and `Message` resources, you can still view both, but in Beta you won't be able to add/remove/edit these resources.


# Authentication

For the duration of the Beta we will be issuing keys that must be sent with every request, at some point in the future we will move to a fuller OAuth implementation.  It's as simple as including a `X-Playr-Key` header with your key as the value for each request.

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/gamertags/leafybasil
```

# Rate Limiting

All users are currently limited to 60 requests per minute, across all calls.  Each response contains the headers `X-RateLimit-Limit` and `X-RateLimit-Remaining` to describe the current allowance.  It's unlikely this limit will be increased during the Beta, but please reach out if you have something special in mind.

```sh
$ curl -i -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/gamertags/leafybasil
HTTP/1.1 200 OK
Server: nginx/1.4.7
Date: Mon, 28 Apr 2014 15:41:35 GMT
Content-Type: application/json
Content-Length: 107
Connection: keep-alive
Status: 200 OK
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 58

{"id":2533274803087262,"gamertag":"leafybasil","users_url":"http://one.playr.io/v1/users/2533274803087262"}
```

# Errors

Any error will return the correct HTTP response code, and also a JSON response that tells you a little more about the problem encountered.

|Code|Cause|
|----|-----------|
|400 |The request you made had invalid/missing parameters.|
|401 |The request is for a private resource or you have not provided a valid API key.|
|403 |The request is for a resource that is disabled during the Beta.|
|500 |Something bad happened, and it wasn't your fault. Please raise an issue!|

```sh
$ curl -i -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/123123123123123/followers
HTTP/1.1 401 Unauthorized
Server: nginx/1.4.7
Date: Mon, 28 Apr 2014 15:43:50 GMT
Content-Type: application/json
Content-Length: 127
Connection: keep-alive
Status: 401 Unauthorized

{"error":"Unauthorized","description":"You must be authenticated as the target user to see their followers list.","status":401}
```

# Resources

## Gamertags

The first resource you will likely encounter is the `Gamertag`, which allows you to retrieve a User ID and a correctly cased representation of the gamertag you submitted.  Users can change their gamertag, but the ID always remains the same, so you may want to store the ID for future use.

> GET /v1/gamertags/:gamertag

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/gamertags/leafybasil

{"id":2533274803087262,"gamertag":"leafybasil","users_url":"http://one.playr.io/v1/users/2533274803087262"}
```

## Users

### Retrieve a profile

> GET /v1/users/:user_id

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/2533274803087262
```

**Response**

```json
{
   "id": 2533274803087262,
   "is_sponsored_user": false,
   "game_display_name": "leafybasil",
   "app_display_name": "leafybasil",
   "gamerscore": 20320,
   "gamertag": "leafybasil",
   "account_tier": "Gold",
   "tenure_level": 7,
   "xbox_one_rep": "GoodPlayer",
   "followers_url": "http://one.playr.io/v1/users/2533274803087262/followers",
   "friends_url": "http://one.playr.io/v1/users/2533274803087262/friends",
   "messages_url": "http://one.playr.io/v1/users/2533274803087262/messages",
   "game_clips_url": "http://one.playr.io/v1/users/2533274803087262/game-clips",
   "recent_players_url": "http://one.playr.io/v1/users/2533274803087262/recent-players",
   "titles_url": "http://one.playr.io/v1/users/2533274803087262/titles",
   "achievements_url": "http://one.playr.io/v1/users/2533274803087262/achievements",
   "status_url": "http://one.playr.io/v1/users/2533274803087262/status",
   "display_pic_url": "http://assets.playr.io/avatars/small/rwljod2fPqLqGP3DBV9F_yK9iuxAt3_MH6tcOnQXTc8rItmG8QAPDKM_fpGigoUIuHtafLWvAgGNZGnkC43ahtEuSFSf.YBpbFdp6D07j8I-",
   "large_display_pic_url": "http://assets.playr.io/avatars/large/rwljod2fPqLqGP3DBV9F_yK9iuxAt3_MH6tcOnQXTc8rItmG8QAPDKM_fpGigoUIuHtafLWvAgGNZGnkC43ahtEuSFSf.YBpbFdp6D07j8I-"
 }

```

### Followers

> GET /v1/users/:user_id/followers

Followers returns a list of users that are following the authenticated user.  This call is restricted by privacy controls, one user cannot see anothers followers.

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/2533274803087262/followers
```

**Response**

```json
[
  {
    "id": 2533274832936274,
    "date_followed": "2014-04-11T16:46:45.1618132Z",
    "users_url": "http://one.playr.io/v1/users/2533274832936274"
  },
  {
    "id": 2535464302280695,
    "date_followed": "2014-03-15T20:13:54.1332813Z",
    "users_url": "http://one.playr.io/v1/users/2535464302280695"
  }
]
```

### Recent Players

> GET /v1/users/:user_id/recent-players

Returns a list of players that the user recently met in games.  This call is restricted by privacy controls, one user cannot see the recent players another has met.

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/2533274803087262/recent-players
```

**Response**

```json
[
  {
    "id": 2533274872465075,
    "is_followed": false,
    "is_following": false,
    "title": {
      "id": 1292135256,
      "name": "Titanfall"
    },
    "date_played": "2014-04-28T22:37:08.3053142Z",
    "users_url": "http://one.playr.io/v1/users/2533274872465075"
  },
  {
    "id": 2533274858670305,
    "is_followed": false,
    "is_following": false,
    "title": {
      "id": 1292135256,
      "name": "Titanfall"
    },
    "date_played": "2014-04-28T22:37:08.3053142Z",
    "users_url": "http://one.playr.io/v1/users/2533274858670305"
  }
]
```

### Status

> GET /v1/users/:user_id/status

Returns the current status for a user, who may be online or offline.

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/2533274803087262/status
```

**Response**

```json
{
  "id": 2533274803087262,
  "is_online": false,
  "status": {
    "platform": "XboxOne",
    "titles": [
      {
       "id": 714681658,
       "name": "Home",
       "date_modified": "2014-04-28T22:54:57.5501074Z"
      }
    ]
  }
}
```

### Game Clips

> GET /v1/users/:user_id/game-clips

```sh
~ curl -H "X-Playr-Key: PUTYOURKEYHERE"  http://one.playr.io/v1/users/2533274803087262/game-clips
```

|Name|Type|Description|
|----|----|-----------|
|title|integer| Filter clips by a title id.|

** Response **
```json
[
  {
    "id": "5c6c9e15-d189-4474-bf01-173b74e3d9af",
    "state": "Published",
    "date_recorded": "2014-04-28T22:42:55Z",
    "date_last_modified": "2014-04-28T22:54:44.3051123Z",
    "type": "UserGenerated",
    "title": {
      "id": 1292135256,
      "name": "Titanfall"
    },
    "duration":28,
    "rating": 0,
    "rating_count": 0,
    "views": 0,
    "saved": false,
    "large_thumbnail_url": "http://assets.playr.io/clips/t3004/0009000000c1a79e-5c6c9e15-d189-4474-bf01-173b74e3d9af/Thumbnail_Large.PNG",
    "small_thumbnail_url": "http://assets.playr.io/clips/t3004/0009000000c1a79e-5c6c9e15-d189-4474-bf01-173b74e3d9af/Thumbnail_Small.PNG",
    "url": "http://assets.playr.io/clips/d3004/asset-3544ef7a-5794-4acd-8d83-b0b10f4ea7c2/GameClip-Original.MP4?sv=2012-02-12&st=2014-04-29T09%3A45%3A09Z&se=2014-04-29T10%3A50%3A09Z&sr=c&sp=r&sig=IqppgeJ%2FtjS6CmEugIaSSeuCMz%2FNVbFEizLhX9E4WPc%3D&__gda__=1398768609_4cf091d6cd5339b2d598944ad211abde",
    "date_expires": "2014-04-29T10:50:09.0452631Z",
    "name": "",
    "game_clip_locale": "en-GB"
  }
]
```

## Titles

> GET /v1/users/:user_id/titles

Returns a list of apps and games that the user has played in the order of most recent.  Provide an optional `type` param to filter the list.

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/2533274803087262/titles
```

|Name|Type|Description|
|----|----|-----------|
|type|string| 'App' or 'Game'|


**Response**

```json
[
  {
    "id": 1509068581,
    "name": "Amazon Instant Video",
    "date_played": "2014-04-28T22:54:57.3313429Z",
    "stats_url": "http://one.playr.io/v1/users/2533274803087262/stats/1509068581",
    "achievements_url": "http://one.playr.io/v1/users/2533274803087262/achievements/1509068581",
    "type":"App",
    "image_url":"http://assets.playr.io/covers/8Oaj9Ryq1G1_p3lLnXlsaZgGzAie6Mnu24_PawYuDYIoH77pJ.X5Z.MqQPibUVTcQ0JPReX7E0ewqTJ3MeiS_tUzJnRtr9m1P_WrX3f5c3ZzJeUxwLFM.s9PH.3whEYkdo0i.pcQdGMZDXbJQFn9IfXXMDzOJI4B0OqMG.cXvTWNKqrTvbCycEyn0xdWQU_hpO80t3l4JZiOMEGvmmXdqJaiCzVXTuHt_8NpoGRULyc-"
  },
  {
   "id": 1292135256,
   "name": "Titanfall",
   "date_played": "2014-04-28T22:54:32.055393Z",
   "stats_url": "http://one.playr.io/v1/users/2533274803087262/stats/1292135256",
   "achievements_url": "http://one.playr.io/v1/users/2533274803087262/achievements/1292135256",
   "type":"Game",
   "image_url":"http://assets.playr.io/covers/8Oaj9Ryq1G1_p3lLnXlsaZgGzAie6Mnu24_PawYuDYIoH77pJ.X5Z.MqQPibUVTchFo8Iv.leBn2QtTr0OOkq5GRmuBEGN.mUZgQeTU1sztFu9Em3E96iKUjUMy8Du.FEzd_xJCwHO9OPWVHmKQKLuczXyMqZ9IgBgG7OOBPgUJTRU77wE.pBNrZnzXa9voVpwxzM9mtZXgFbrGq9srOor0tPPTBDBm3GFxkCT8xCXw-"
  }
]
```

### Stats

Stats can vary from title to title, also their values are somewhat arbitrary.  Below you can see my stats for Titanfall, 'Player Generation' starts at 0 for first gen, so even though the value is 2, I am third gen (at time of writing!).

> GET /v1/users/:user_id/titles/:title_id/stats

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/2533274803087262/titles/1292135256/stats
```

```json
{
  "user": {
    "id": 2533274803087262
  },
  "title": {
    "id": 1292135256,
    "achievements_url": "http://one.playr.io/v1/users/2533274803087262/titles/1509068581/achievements"
  },
  "stats": [
    {
      "name": "Player Generation",
      "type": "Integer",
      "value": 2
    },
    {
      "name": "Player Level",
      "type": "Integer",
      "value": 45
    },
    {
      "name": "Matches Completed",
      "type": "Integer",
      "value": 279
    },
    {
      "name": "MinutesPlayed",
      "type": "Integer",
      "value": 3155
    }
]
```

### Achievements

> GET /v1/users/:user_id/titles/:title_id/achievements

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/2533274803087262/titles/1509068581/achievements
```

```json
[
  {
    "id": 11,
    "type": "Persistent",
    "description": "Watched 5 Drama titles",
    "is_secret": false,
    "name": "I’m the king of the world!",
    "state": "Achieved",
    "participation_type": "Individual",
    "date_achieved": "2014-04-23T17:27:08.5093360Z",
    "title": {
      "id": 1509068581,
      "name": "Amazon Instant Video",
      "achievements_url": "http://one.playr.io/v1/users/2533274803087262/titles/1509068581/achievements"
    }
  }
]  
```

## Friends

> GET /v1/users/:user_id/friends

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/2533274803087262/friends
```

**Response**

```json
[
  {
    "id": 2535464302280695,
    "is_followed": true,
    "users_url": "http://one.playr.io/v1/users/2535464302280695",
    "legacy_platform_friend": false,
    "is_following": true
  },
  {
    "id": 2533274811892679,
    "is_followed": true,
    "users_url": "http://one.playr.io/v1/users/2533274811892679",
    "legacy_platform_friend": true,
    "is_following": true
  }
]
```

## Messages

### List Messages

> GET /v1/users/:user_id/messages

```sh
curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/2533274803087262/messages
```

```json
[
  {
    "id": 677,
    "sender": {
      "id": 2533274805386197,
      "gamertag": "EJStyleS"
    },
    "date_sent": "2014-04-15T21:35:32Z",
    "date_expires": "2014-05-15T21:35:32Z",
    "is_read": true,
    "summary": "Cold...",
    "message_url": "http://one.playr.io/v1/users/2533274803087262/messages/677"
  }
]
```

### Retrieve a message

> GET /v1/users/:user_id/messages/:message_id

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/2533274803087262/messages/677
```

**Response**

```json
{
  "sender": {
    "id": 2533274805386197,
    "gamertag": "EJStyleS"
  },
  "date_sent": "2014-04-15T21:35:32Z",
  "date_expires": "2014-05-15T21:35:32Z",
  "body": "Cold..."
}
```

## Achievements

> GET /v1/users/:user_id/achievements

Retrieves a summary of achievements for the user, a more detailed breakdown for each title can be obtained from the title achievements endpoint.

```sh
$ curl -H "X-Playr-Key: PUTYOURKEYHERE" http://one.playr.io/v1/users/2533274803087262/achievements
```

```json
[
  {
    "title": {
      "id": 122001257,
      "name": "YouTube"
    },
    "earned_achievements": 1,
    "earned_gamerscore": 0,
    "total_gamerscore": 0,
    "title_achievements_url": "http://one.playr.io/v1/users/2533274803087262/achievements/122001257"
  },
  {
    "title": {
      "id": 1292135256,
      "name": "Titanfall"
    },
    "earned_achievements": 20,
    "earned_gamerscore": 605,
    "total_gamerscore": 1000,
    "title_achievements_url": "http://one.playr.io/v1/users/2533274803087262/achievements/1292135256"
  }
]
```
