# SQL-instagram-project

USE ig_clone;
Select * from comments;
Select * from followed;

Select * from likes;
Select * from photos;
Select * from photo_tags;
Select * from tags;
Select * from users;


-- Part 1
-- We want to reward our users who have been around the longest. Find the 10 oldest users.

/* Out of all users, awarding the top 10 oldest users over platform */

SELECT DATE(created_at), username
FROM users
ORDER BY DATE(created_at)
LIMIT 10;

/* To find the oldest 10 users on the platform, the date was extracted from the created_at column, the data was arranged in ascending order 
and top 10 users were limited */


-- Part 2
/* What day of the week do most users register on? We need to figure out when to schedule an ad campaign */

SELECT DAYNAME(created_at) as week_day, count(username) as count_username
FROM users
GROUP BY week_day
ORDER BY count_username DESC;

/* We first selected weekday name from created_at column and counted the username under each weekday, then grouped by week_day 
and ordered in descending order to print the most registered users count on top */


-- Part 3
/* We want to target our inactive users with an email campaign. Find the users who have never posted a photo */

SELECT u.username, u.id
FROM users u
	LEFT JOIN photos p
		ON u.id = p.user_id
WHERE p.user_id is NULL;

/* We first selected username and id from users with a left join on photos and extracted only those rows having NULL values in user_id 
from photos table */


-- Part 4
/*We're running a new contest to see who can get the most likes on a single photo. WHO WON??!!*/

SELECT p.user_id, u.username, l.photo_id, count(l.user_id) as total_likes
FROM likes l
	JOIN photos p
		ON l.photo_id = p.id
	JOIN users u
		ON p.user_id = u.id
GROUP BY l.photo_id
ORDER BY total_likes DESC
LIMIT 1;


-- Part 5
/*Our Investors want to know... How many times does the average user post? total number of photos/total number of users*/

SELECT (SELECT COUNT(id) FROM photos)/(SELECT COUNT(id) FROM users) as average_user_post;


-- Part 6
/*user ranking by postings higher to lower*/

SELECT u.id, u.username, COUNT(p.id) as cnt
FROM users u
	LEFT JOIN photos p
		ON u.id = p.user_id
GROUP BY u.id
ORDER BY cnt DESC;


-- Part 7
/*total numbers of users who have posted at least one time */
-- u.id, u.username, COUNT(p.id) as cnt

SELECT COUNT(DISTINCT(user_id)) FROM photos;


-- Part 8
/*A brand wants to know which hashtags to use in a post
What are the top 5 most commonly used hashtags?*/

select tag_name, count(tag_name) 
from tags t
join photo_tags p
on t.id = p.tag_id
group by t.id 
order by count(tag_name) desc
limit 5;


-- Part 9
/*We have a small problem with bots on our site...
Find users who have liked every single photo on the site*/

select u.id, count(u.id) AS total
from users u
join likes l
on l.user_id = u.id
group by u.id
having total = (select count(*) from photos);


-- Part 10
/*We also have a problem with celebrities
Find users who have never commented on a photo*/

select distinct u.id, u.username
from users u
left join comments c
on c.user_id = u.id
where comment_text is null;


-- Part 11
/*Mega Challenges
Are we overrun with bots and celebrity accounts?
Find the percentage of our users who have either never commented on a photo or have commented on every photo*/

select (count(distinct ab.id)/(select count(distinct id) from users))*100 as perc_comment_every_photo, 
(count(distinct cd.id))/(select count(distinct id) from users)*100 as perc_null_comment from 
(select distinct u.id, u.username
from users u
join comments c
on c.user_id = u.id
group by u.id
having count(comment_text) = (select count(distinct photo_id) from comments)) as ab
join
(select distinct u.id, u.username
from users u
left join comments c
on c.user_id = u.id
where comment_text is null) as cd;


-- Part 12
/*Find users who have ever commented on a photo*/
select distinct u.id, u.username
from users u
left join comments c
on c.user_id = u.id
where comment_text is not null;


-- Part 13
/*Are we overrun with bots and celebrity accounts?
Find the percentage of our users who have either never commented on a photo or have commented on photos before*/

select (count(distinct ab.id)/(select count(distinct id) from users))*100 as perc_comment_every_photo, 
(count(distinct cd.id))/(select count(distinct id) from users)*100 as perc_null_comment from 
(select distinct u.id, u.username
from users u
join comments c
on c.user_id = u.id
group by u.id
having count(comment_text) = (select count(distinct photo_id) from comments)) as ab
join
(select distinct u.id, u.username
from users u
left join comments c
on c.user_id = u.id
where comment_text is not null) as cd;    


-- Part 14
/*Find users who have ever commented on a photo*/
SELECT username,comment_text
				FROM users
				LEFT JOIN comments ON users.id = comments.user_id

				HAVING comment_text IS NOT NULL;
