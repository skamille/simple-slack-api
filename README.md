[![Build Status](https://travis-ci.org/Ullink/simple-slack-api.svg?branch=master)](https://travis-ci.org/Ullink/simple-slack-api)
[![GitHub release](https://img.shields.io/github/release/Ullink/simple-slack-api.svg)](https://github.com/Ullink/simple-slack-api/releases)
[![Join the chat at https://gitter.im/Ullink/simple-slack-api](https://badges.gitter.im/Ullink/simple-slack-api.svg)](https://gitter.im/Ullink/simple-slack-api?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

# Simple Slack API

This library allows an application to connect to [Slack](http://www.slack.com/) to receive and send messages from any channel as if it were a slack client.

The main purpose of this library is to build Slack bots able to react to channel events without having to configure any web hook.

With this library you should be able to connect to Slack and to send some messages in less than 20 lines (no one-liners).

## Maven/Gradle

You can add this library as a dependency to your Maven or Gradle project through [JitPack](https://jitpack.io/#Ullink/simple-slack-api).

## How to use it ?

The SlackSession interface provides various methods to send/modify and delete messages on a given channel :
```java
SlackMessageHandle sendMessage(SlackChannel channel, String message, SlackAttachment attachment);
SlackMessageHandle sendMessage(SlackChannel channel, String message, SlackAttachment attachment, SlackChatConfiguration chatConfiguration);
SlackMessageHandle updateMessage(String timeStamp, SlackChannel channel, String message);
SlackMessageHandle deleteMessage(String timeStamp, SlackChannel channel)
```        
(Since v0.4.3, emoji reactions on channel messages)

The SlackSession interface provides a method to add an emoji reaction to a message on a given channel :
```java
SlackMessageHandle addReactionToMessage(SlackChannel channel, String messageTimeStamp, String emojiCode);
```        
 
## Full example :

Here's a full example with an echoing bot using this library :
```java
public class Example
{

  public static void main(String[] args) throws Exception
  {

    final SlackSession session = SlackSessionFactory.
      createWebSocketSlackSession("authenticationtoken", Proxy.Type.HTTP, "myproxy", 1234);
    session.addMessagePostedListener(new SlackMessagePostedListener()
    {
        @Override
        public void onEvent(SlackMessagePosted event, SlackSession session)
        {
          SlackChannel target = event.getChannel();
          //let's send a message
          SlackMessageHandle<SlackMessageReply> handle = session.sendMessage(target,
                              event.getMessageContent(), null);
          try
          {
              Thread.sleep(2000);
          } catch (InterruptedException e)
          {
              e.printStackTrace();
          }
          //2 secs later, let's update the message (I can only update my own messages)
          session.updateMessage(handle.getReply().getTimestamp(),target,
                                event.getMessageContent()+" UPDATED");
          try
          {
              Thread.sleep(2000);
          } catch (InterruptedException e)
          {
              e.printStackTrace();
          }
          //2 secs later, let's now delete the message (I can only delete my own messages)
          session.deleteMessage(handle.getReply().getTimestamp(),target)
        }
      });
    session.connect();

    while (true)
    {
      Thread.sleep(1000);
    }
  }
}
```        

## Features

### Supported Slack commands

All these commands can be sent through the library provided your bot has the rights to (IE : the bot has to be a member of the group or the channel to post some messages on it)

* Post a message on a channel / private group / user
* Update a message
* Delete a message
* Join a channel
* Leave a channel / a private group
* Invite to a channel / a private group
* Archive a channel
* Open a direct message channel (since v0.5.0)
* Open a multiparty direct message channel (since v0.5.0)
* Get the presence status of a user
* Add a reaction emoji to a message
* Fetch the channel history (since v0.5.0) (needs full user credentials)
* Invite a user to Slack (since v0.5.0)

On top of these built-in features, it is now possible to send not yet implemented API calls by using the postGenericSlackCommand method : 

```java
postGenericSlackCommand(Map<String, String> params, String command);
```


### Supported Slack events

All these events can be listen provided your bot has the rights to (IE : the bot has to be a member of the group or a channel to gets the message events related to it)

* message posted event
* message deleted event
* message updated event
* channel created event
* channel / private group deleted event
* channel / private group archived event
* channel / private group unarchived event
* channel / private group renamed event
* channel /private group joined event
* reaction added to message event (since v0.5.0)
* reaction removed from message event (since v0.5.0)


# Thanks

Many thanks to everyone who has contributed to this library :

* Christian Sprecher
* Jan Vidar Krey
* David Seebacher
* Wouter Vernaillen
* Claudio Comandini
* Maxim Gurkin
* Ole Kozaczenko
* Georges Gomes
* François Valdy
* Harry Fox
* Logan Clément

(Let me know if I forgot someone, I'll fix that ASAP ;) )

# License

This library is licensed under the [Creative Commons CC0 1.0 Universal](http://creativecommons.org/publicdomain/zero/1.0/) license with no warranty (expressed or implied) for any purpose.
