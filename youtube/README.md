Youtube solves 3 major problems
1. massive video storage:
2. global low latency: how are they able to deliver videos quickly to the end user
3. write-heavy meta data: every video has views, likes, share, subscribres, anayltics 

layer 1
upload path

1. raw video -> S3/GCS -> blob not in DB -> message queue -> transcoding pipeline -> creates different resolution 144p-4K and different formats AV1, VP9, H.294

does the message queue offer other functionality other than transcoding?

why different format?

H.264 - the baseline 
nearly every device and decode H/264
biggest file size 
high compression time
fallback tier for devices which do not understanding modern encoding

VP9
google own codec mid-tier therefore no patent licensing costs at scale

AV1 - newest most efficient and the most expensive to encode

why transcoding is decoupled from upload request?

what is the difference between encryption and encoding?

what is UTF-8 encoding?
- turning text characters into byte
- unicode assign every character in every langauge symbols, emojis a unique number called code point

what is the difference between UTF-8, UTF-16, and UTF-32?

layer 2

video files are immutable?
but youtube offer inbuild editor which allows to trim and edit vidoes so does that mean videos immutable?

metadata for videos such as views, likes, comments are build using a mixture of SQL and NoSQL

for what youtube feature is build using SQL and which ones are build using NoSQL

layer 3
videos aren't served from the origin storage direclty.
Instead uses CDN - google global cache

what is adaptive bitrate streaming DASH/HLS

Ohter components for youtube are
1. search
2. recommendation


in youtube which features are read-heavy and which are write-heavy and why?

in youtube which features use eventual consistency and which one use strict consistency and why?


17 July 2026

what is colossus?
what is multiple container/segment format for streaming means?

what are codecs?
AV1 compresses 30% better than VP9/H.264 but cost more CPU to encode
AV1 is used for popular video

does youtube only keep the lower resolution footage for videos that are hardly watched?

how many copies of the same video does youtube create as backup?

why encode a youtube video?
encoding just means compressing a youtube video

encoding solves two problem
1. storage - a codec throws away redundnt information a mostly-static background barely changes frame-to-frame, so encoders only store the differences, not every full frame) and compresses what's left. 
2. Streaming/bandwidth: a compression file can be sent over the network
3. cost: because storing large file would need more memory



1. Raw uploads

raw video -> blob(s3) -> DB stors the ptr to the blob along with videoID, storagepath, owner, status 

2. transcoding = storage multiplication done async

one upload
- multiple resolutions 144p - 4k
- multiple codecs H.264, VP9, AV1
- mutiple container/segment

encoding is CPU heavy and slow

fan-out: upload trigger -> thumb nail generation, content-ID/copyright-scanning -> moderation checks

3. dedupliaction and compression at scale

a. identical or near-identical uploads (re-uploads, pirated re-encodes) can be detected via hashing/fingerprinting and reference existing transcodes instead of storing duplicates.

b. tirered storage
hot/popular videos sits in faster storage
less frequently watched videos are stored in cheaper and slower storage

c. erasure coding not full replication

split data into fragments + parity, reconstructable from a subset. Gets similar durability at ~1.5x overhead instead of 3x






