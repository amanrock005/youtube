Fresher level

1. how would you design the high-level architecture for uploaidng and viewing a video?

upload(raw footage with metadata such as title, description, langauge) -> upload service(API) -> Blob(S3, Google cloud storage) -> DB (metadata + URL to the Blob) -> playback service/CDN


2. why can't you just store video files directly in a SQL DB and where should they go instead?

cost: storing large file require lot of memory
efficienty: searching, indexing becomes difficult and slow
size mismatch: DB are meant for smaller, structure rows(number,strings)
access: You cannot perform joins, where or having clause on video files


3. what happens when a users hits plays on a video?

client sends video ID server -> DB get metadata and video URL (not the raw footage but the CDN) -> video is streamed and not downloaded so that the user doesn't have to wait to watch the video after downloading


3 YOE

1. design the video upload & processing pipeline(wit transcoding)

Reqirements to make the uploading + transcoding process at large scale we need to keep in mind

- support large video files (few gbs to 100gbs)
- convert the raw footage to multiple resoultions and formats
- should not block the user while their uploading the video


1. the upload process is broken into chunks so when are the chunks reconslidated 
2. what happens if writing to db failes 
3. what happens if some chunks fail to encoded 
3. what happens if raw footaage fails to be uploaded as blob



3. design the view count and comments system at scale

views
- do not increment DB counter on every single view it will massive write contention(hot row problem)
- each server increments local  in-memory counter or writes to a light weight message queue
- stream processing job (kafka consumer/flink) aggregate teh count and batches updates to the DB

what is stream processing job and what can't a cron job do the task of updating the count of vidos?

comments
- comments are write-moderate and read heavy
- NoSQL keyed by video_id 
- stored by timestamp ie fetch the latest comments
- use pagination
- comment count follows the same logic as view count
- top comments(momst liked comments) separate sorted strcture (e.g., a sorted set in Redis per video) rather than sorting on read.


