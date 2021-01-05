# Research

## Table of Contents
* [Intro / Motivation 😕](#intro--motivation-)
* [Data retrieval](#data-retrieval)
* [How do we export our dialog/chat](#how-do-we-export-our-dialogchat)
* [Each message contains fields:](#each-message-contains-fields)
* [Real world view VS Extracted data view](#real-world-view-vs-extracted-data-view)
* [Authors](#authors)
## Intro / Motivation 😕

Nowadays, advertisement has become an integral part of business as such.

It is no longer a question of whether one needs to promote their business or not.

The question now is how and where to promote one's product.

There is a variety of methods and facilities for advertisement.

According to **Instamber**: *"**telegram marketing can be a productive method to promote your business, as you have millions of active Telegram users around the globe**."*

Nevertheless, all the given articles' methods assume those entrepreneurs would either spend lots of time developing their channel or pay money to have their ads spread.

So we come with an alternative approach to promoting a business via Telegram.

Let us assume that we have to promote paid CS courses and materials like books, site subscriptions via Telegram at no cost. In other words, we can pay popular channels for ads and have no time to spare developing any channels. So, naturally, we would choose thematic groups for promoting our products. For example, we can take a Python programming group.

It is the right choice because the majority of members of this group are interested in CS. Everything seems to be perfect for our purpose, but there is usually a low level of spam tolerance, which ads are considered to be. So we need to embed the advertisement in the message and send them in a way that will not make admins suspect us of spamming or advertising.

The way of embedding an advertisement into a message is neither a matter of probability theory nor statistics.

Let us assume that we come up with an idea of how to do that. Nevertheless, our plan might have drawbacks. For example, admins can be overly suspicious and uncover our scheme if we send too many messages with a hidden advertisement. However, that is not a big problem for us, and we can send one message a week, not risking being kicked from the group.

So, an interesting question arises, **on what day of the week and at what time should we send hidden ads to influence as many group members as possible.**

This is where the Statistics may come in handy.


## Data retrieval

For this mini-research, we will be using our own retrieved data from a relatively big (~10k members) Telegram channel.

The channel specializes in programming, specifically Python, and is mostly in the Russian language, however because of being the tech chat, there are lots of daily active users, which is what we exactly need.

### How do we export our dialog/chat

One of the team members has been working on a private project that processes messages in telegram chats/dialogs/channels.

We are using that small tool to download all the data we need.

It is written in Python and mainly depends on [`telethon` package for Python.](https://pypi.org/project/Telethon/)

We also generated an API keys, having which we could access any message in any chat/channel/dialog (if only our acc. is the valid member)

In our case it is messages in a specific chat "Python" (we also could use any other chat/channel or a dialog)

**The snippet of the actual data retrieval function:**

```python
async def download_dialog(client, id, MSG_LIMIT):
    try:
        tg_entity = await client.get_entity(id)
        messages = await client.get_messages(tg_entity, limit=MSG_LIMIT)
    except ValueError:
        errmsg = f"No such ID found: #{id}"
        raise ValueError(errmsg,)

    dialog = []

    for m in messages:

        msg_attrs = msg_handler(m)

        dialog.append(
            {
                "id": m.id,
                "date": m.date,
                "from_id": m.from_id,
                "to_id": msg_attrs["to_id"],
                "fwd_from": m.fwd_from,
                "message": msg_attrs["message"],
                "type": msg_attrs["type"],
                "duration": msg_attrs["duration"],
            }
        )

    dialog_file_path = os.path.join(config["dialogs_data_folder"], f"{str(id)}.csv")

    df = pd.DataFrame(dialog)
    df.to_csv(dialog_file_path)
```

---

Let's first take an overview of the data we have. So in the end, our dateset contains every message chronologically ordered.

### Each message contains fields:

- `id` — the id of the message
- `message` — the actual message
- `date` — the precise date & time
- `from_id` — the id of a user which sent the message
- `to_id` — the id of the user to which the message was sent
- `type` — type of message [sticker/video/voice]
- `duration` — if type of video/voice then its duration
- `fwd_from` — the id of the user from which the message was forwarded

### Real world view *VS* Extracted data view

You can view raw data here [link](https://www.dropbox.com/s/7rykwc27tq2v1ic/data.csv?dl=0)

The same message string *`"Сваггер схемы, прото файлы вполне могут быть"`* in Telegram application and in extracted .csv file.

![Screenshot of Telegram desktop](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/f5e1d78a-efad-49bb-8951-789710402289/Screenshot_from_2021-01-05_00-31-42.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210105%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210105T172446Z&X-Amz-Expires=86400&X-Amz-Signature=0a4fded63b84a07343789ef4ebd99391c8f77d15984de47ae24e05c0ab4fcddf&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Screenshot_from_2021-01-05_00-31-42.png%22)

| id  | date                      | from_id                     | to_id                              | fwd_from                                                                                             | message | type | duration |
|-----|---------------------------|-----------------------------|------------------------------------|------------------------------------------------------------------------------------------------------|---------|------|----------|
| ... | ...                       | ...                         | ...                                | ...                                                                                                  | ...     | ...  | ...      |
| 112 | 2021-01-04 16:46:16+00:00 | PeerUser(user_id=123109378) | PeerChannel(channel_id=1007166727) | Спасибо. В таком случае немного раздражает в каждом сервисе писать сериализацию/десереализацию       | text    |      |          |
| 116 | 2021-01-04 16:39:07+00:00 | PeerUser(user_id=214334796) | PeerChannel(channel_id=1007166727) | А зачем общие дто, если бд разные, и языки могут быть разные? Общие .proto файлы или схемы разве что | text    |      |          |
| 115 | 2021-01-04 16:40:29+00:00 | PeerUser(user_id=123109378) | PeerChannel(channel_id=1007166727) | Ну возможно, если обобщить мой вопрос то: как описывают и следят за контрактами на уровне сервисов   | text    |      |          |
| 114 | 2021-01-04 16:41:21+00:00 | PeerUser(user_id=214334796) | PeerChannel(channel_id=1007166727) | Документацией, end to end тестами, общими схемами                                                    | text    |      |          |
| 113 | 2021-01-04 16:43:04+00:00 | PeerUser(user_id=43022119)  | PeerChannel(channel_id=1007166727) | Сваггер схемы, прото файлы вполне могут быть                                                         | text    |      |          |
| ... | ...                       | ...                         | ...                                | ...                                                                                                  | ...     | ...  | ...      |


## Authors

- Max Bilyk - [github](https://github.com/MaxBi2019)
- Andrew Bek - [github](https://github.com/ReyBroncas)
