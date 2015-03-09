---
layout: post
title: "To Blog or Not To Blog"
date: 2014-08-14 -0800
comments: false
categories: [meta]
---

# Blogging like speed chess

So I'm having a tough time managing my time.  There are a boat load of legitimate needs that take priority over writing about technical subjects.  It's tough to argue with a two month old that you just need to scribble down a few more thoughts before you can get her more milk.

So now I'm hunting for blogging methodologies that minimize time commitment.  It limits my ability to create robust long-form content but I'm probably not up to much of that anyway at this stage in my blogging career.  Let's examine a few of my favorite examples.

First up, is Chirs Coiyer.  He has a nice mix of content lengths at http://css-tricks.com/.  Occasionally, he drops an ultra-short post about something interesting that he's read.

> It's a fun little soundbite to talk about how the web is responsive right out of the box. With no authored CSS at all, a website will flow to whatever screen width is available. If your site isn't responsive, you broke it.
> 
> Well that's almost true, but as Adam Morse says in this new project:
> 
> > HTML is almost 100% responsive out of the box. These 115 bytes of css fix the 'almost' part.
> 
> Things like images and tables can have a set widths that would force a layout wider than a viewport. And of course, the [meta tag](http://css-tricks.com/snippets/html/responsive-meta-tag/).
> 
> And look at that TLD!
> 
> [Direct Link](http://fluidity.sexy/)

This is super-quick content curation at it's best.  The whole post is about 100 words and probably took less than 15 minutes to put together.  It also has a lot of value because Chris is raising awareness of a useful trick as well as an interesting author.

Second on my list of interesting authors is Ayende Rahien.  The size and polish of his posts varies; sometimes he's got really clean long form content but oftentimes he's willing to just throw out something in a more stream of consciousness style.  His series on [Go-Raft](http://ayende.com/blog/165858/reviewing-go-raft-part-i) is a good example of that.

Ayende makes it clear that he's exploring a new project, he outlines his methodology (reading through everything from A to Z, top to bottom), then dives in with his analysis.

> http_transporter.go is next, and is a blow to my hope that this will do a one way messaging system. I’m thinking about doing Raft over ZeroMQ or NanoMSG. Here is the actual process of sending data over the wire:
> 
>     // Sends an AppendEntries RPC to a peer.
>     func (t *HTTPTransporter) SendAppendEntriesRequest(server Server, peer *Peer, req *AppendEntriesRequest) *AppendEntriesResponse {
>         var b bytes.Buffer
>         if _, err := req.Encode(&b); err != nil {
>             traceln("transporter.ae.encoding.error:", err)
>             return nil
>         }
>     
>         url := joinPath(peer.ConnectionString, t.AppendEntriesPath())
>         traceln(server.Name(), "POST", url)
>     
>         t.Transport.ResponseHeaderTimeout = server.ElectionTimeout()
>         httpResp, err := t.httpClient.Post(url, "application/protobuf", &b)
>         if httpResp == nil || err != nil {
>             traceln("transporter.ae.response.error:", err)
>             return nil
>         }
>         defer httpResp.Body.Close()
>     
>         resp := &AppendEntriesResponse{}
>         if _, err = resp.Decode(httpResp.Body); err != nil && err != io.EOF {
>             traceln("transporter.ae.decoding.error:", err)
>             return nil
>         }
>     
>         return resp
>     }
> 
> This is very familiar territory for me, I have to say :). Although, again, there is a lot of wasted memory here by encoding the data multiple times, instead of streaming it directly.

He writes as he thinks and with minimal editing.  I can see him, in my mind's eye, typing up the blog post as he reads through the codebase.  This keeps the effort to O(1) and allows him to produce ~1200 words of content.  Not a fantastic fit for my lifestyle at home since I don't have the dedicated time to draft that kind of analysis as I go but it might do for blogging work related things.  I'm assuming that this was done in a single evening sometime after dinner given his closing:

> And I think that this is enough for now… it is close to 9 PM, and I need to do other things as well. I’ll get back to this in my next post.

And with that, I'm going to follow in Ayende's footsteps and get back to other things that need doing.