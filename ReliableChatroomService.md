# Distributed chatroom application

<!-- MarkdownTOC -->

- [Tell me about this project](#tell-me-about-this-project)
	- [Describe](#describe)
	- [Most challenging part](#most-challenging-part)

<!-- /MarkdownTOC -->


## Tell me about this project
### Describe
* For this project I developed distributed chatroom service to support join/leave chatroom, message like/dislike and chatroom history retreival features. I implemented a reliable multicasting service with 40Mbps throughput under 5% package loss environment. Based on the multicasting service, I implemented master-to-master replication and server-side commit logs for high availability and self-recovery. Finally, I implemented anti-entropy algorithms for eventual consistency after network partitions have been merged.  

### Most challenging part
* Messaging topic design: How to send messages efficiently under cases like machine crashes and network partitions.