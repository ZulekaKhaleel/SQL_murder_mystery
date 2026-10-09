# SQL Murder Mystery Solution

## The crime
A crime has taken place and the detective needs your help. 
The detective gave you the crime scene report, but you somehow lost it. 
You vaguely remember that the crime was a ​murder​ that occurred sometime on ​Jan.15, 2018​ and that it took place in ​SQL City​. 

## How I solved it
1. First, I looked up the crime scene to fint the witnesses:
   ''''SELECT * 
FROM crime_scene_report 
WHERE date = 20180115 
  AND type = 'murder' 
  AND city = 'SQL City';
'''
## Here's what I found using the above query:
Date: 20180115
Type: murder
Description: Security footage shows that there were 2 witnesses.
The first witness lives at the last house on "Northwestern Dr".
The second witness, named Annabel, lives somewhere on "Franklin Ave".
City: SQL City

2. Next, I searched about witnesses using:
   '''SELECT * 
FROM interview 
WHERE person_id IN (
    SELECT id FROM person WHERE address_street_name = 'Northwestern Dr'
    UNION
    SELECT id FROM person WHERE name LIKE 'Annabel%' AND address_street_name = 'Franklin Ave'
);
'''
## What I got:
I found a bunch of details but there were 2 witnesses who described the crime.
1. 14887: I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag.
   The membership number on the bag started with "48Z".
   Only gold members have those bags. The man got into a car with a plate that included "H42W".
2. 16371: I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th.
4. (continue for each step)

3. Who did it and how I know:
'''
SELECT p.name, p.id, p.license_id, dl.plate_number
FROM person p
JOIN drivers_license dl ON p.license_id = dl.id
JOIN get_fit_now_member gfnm ON p.id = gfnm.person_id
JOIN get_fit_now_check_in gfnc ON gfnm.id = gfnc.membership_id
WHERE gfnc.check_in_date = 20180109
AND gfnm.id LIKE '48Z%'
AND dl.plate_number LIKE '%H42W%';
              '''
## I found the exact match with plate number and the license id witnessed by the witnesses and it was Jeremy Bowers.

but wait, there's more!

4.I found Jeremy Bower's Transcript using:
'''SELECT transcript FROM interview WHERE person_id = 67318;'''

and He said:
"I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). 
She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017."

5. So I tried to find out who it was using the clues given by Jeremy by using this query:
   '''SELECT p.name, p.license_id
FROM person p
JOIN drivers_license dl ON p.license_id = dl.id
JOIN facebook_event_checkin fec ON p.id = fec.person_id
WHERE dl.height BETWEEN 65 AND 67
AND dl.hair_color = 'red'
AND dl.car_model = 'Model S'
AND dl.car_make = 'Tesla'
AND fec.event_name = 'SQL Symphony Concert'
AND fec.date BETWEEN '20170101' AND '20171231';
''''
And found out it was Miranda Priestly who was the brains behind the murder!


