---
date: 2020-05-27
layout: post
title: Slack threads are one honking great idea -- let's use more of those!
...

<style>
@media screen and (min-width: 800px) {
  img {
    height: 50%;
    width: 50%;
    margin-left: 0;
  }
}
</style>


[Slack][slack], one of the world's most popular business communication platforms, launched the threaded conversations feature [over 3 years ago][launch]. To this day, there are still people who don't use them, be either for inertia, personal taste, or because they don't understand its purpose. The goal of this article is to illustrate that by doing the effortless action of clicking the button to Start/View a thread when answering a message, you will be improving not only the life of your future self but doing a huge favor to all your colleagues.

Threaded messages are not just a communication style. They rely on one single pillar to improve the chat tool usability: reduce distractions by giving freedom to their users. Freedom in the sense that they can choose which conversation streams to follow, without having to leave or mute channels - things that may not be wanted or even feasible. And there are many nice side-effects to get in return. Let's go through some Slack messaging characteristics to better understand their implications.

# There's no need to send multiple separate messages

Which of the following examples is easier to understand:

A bunch of messages sent separately?

![][multi]

Or a single message containing all the needed information:

![][single]

It's important to notice that the second example directly benefits the usage of threads. The messages that originated it are not scattered around. Also, if you need to append more information, the message may be edited (depending on the Workspace settings). That's not just aesthetically pleasing, the main issue is that...

# Every message sent to a channel is a potential disruption

A channel message may not result in a notification sent to your cellphone or desktop browser, but there are a couple of implications. First, there's the "unread messages" icon, where the tab favicon turns white. This icon per se can catch someone else's attention, making them wonder whether their assistance is needed or not. Second, there's the problem that everybody will have to catch up with all channel messages when they return after being away from the chat. By using threads, the number of channel messages is reduced, making it easier for people to skim through the unread ones, choosing what they need to follow.

# Be careful when using the "also send to #channel" option

There's an option to also send the message to channel when replying to a thread. It should be used with care, for the reasons mentioned above: it will generate a channel message that comes with all its implications. It's fine to use it, for instance, when sending a reminder to a thread that was started a while ago and needs attention from people that might have not seen it. Selecting this option just to "make a point", showing what are you are answering to people that might not be interested in the thread may sound condescending and should be avoided.

# A thread is a group of related messages

The main goal of using threads - grouping related messages - facilitates a few use cases. A thread can be, for instance, a support request from another team. After the issue is solved, one can tag it with a checkmark emoji indicating that it was concluded.

![][done]

This can either help someone else taking the shift in understanding if any action is needed or an interested third-party to figure if the problem was properly answered/addressed without going through all the messages. Without a thread, it's hard - impossible in high-traffic channels - to even figure where the conversation ended.

# Threads improve message history significantly

Another situation greatly improved by threads is when going through the message history, which is especially useful in the paid - and unlimited - Slack version. Either by using the search or going through a link, when finding the relevant thread all the information is in there: the parent message containing the whole context, all the discussion properly indicating where it started and where it ended. The true value of that can be easily seen, for instance, when a link to discussion is attached to a ticket in the issue tracker and accessed months later.

# Closing thoughts

Threads were invented with a noble goal: to make text-based communication more efficient. Even if it might be tempting to take a shortcut and start typing a response when you see a message in a channel, remember that clicking on the Start/View thread button is a small step for you, but a giant leap for whole chatting experience. By doing that the life quality of everyone that might be involved in a Slack conversation, either at that exact point in time or in a long time in the future, will be greatly improved.


[done]: /images/2020/slack-done.png
[launch]: https://techcrunch.com/2017/01/18/slack-launches-threaded-messaging-to-take-conversations-off-to-the-side/
[multi]: /images/2020/slack-multi.png
[single]: /images/2020/slack-single.png
[slack]: https://slack.com/
