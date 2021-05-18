# One month of building GoodJobs

About a month ago, I finished up the MVP for [GoodJobs](https://goodjobs.careers), and deployed it to the world. GoodJobs is a job board that lists positions at companies trying to solve problems like food insecurity and climate change, among other important things. This is a project I've really enjoyed working on, and the first (relative) success that I've had with a side-project like this. I want to share a few thoughts about the process, and highlight some things I've learned in the past month.

Here we go.

## Why a job board?

Let's start with some context. I've been really interested in climate tech projects over the last year or so, enough that during my last job search I applied to a handful of companies that are working on fixing climate change. I didn't end up getting hired at any of them, but the process of finding and vetting these companies stuck with me. Lots of companies outwardly signal that they're interested in working toward solutions for problems like climate change.

Funnily enough, I found that a lot of companies in the oil and gas industries had the most 'green' sounding job postings (go figure). While I'm sure there are gains to be made in those industries, personally I wanted to interview with companies who are working on exciting _new_ solutions, preferably something without any vested interest to the status quo. After a few weeks on the hunt, I had developed an added sense for which companies seemed to fit with my outlook, and which ones seemed to be greenwashing.

Fast forward a few months. The perfect weekend presents itself for a bit of side-project hacking, and I'm still thinking about the process of finding interesting jobs with startups in the climate tech space. I really liked the idea of solving that bit of friction I experienced, while potentially helping match talented engineers with companies solving important problems. The decision was made.

## Minimum means minimum

A big problem I've had in the past with projects like this is they never make it out of the dark recesses of VS Code. I'm a programmer, and I fit the stereotype. Lots of tweaking, rearchitecting, refactoring, and then the project dies in the archive folder. This time I opted for a challenge: do away with everything superfluous. No backend, no database, just a site and a flat file with a list of companies. I built the initial site with Svelte + Bulma, with a Node script to scrape some jobs from the HackerNews whoishiring thread into a JSON file. I manually cleaned up the data, and vetted all the positions by clicking through and reading the job descriptions. I added some structure to the metadata, like whether the job was full-time, remote, etc.

And then I launched it.

It felt a little wrong, to be honest. The first deploy had something like 25 jobs, no descriptions of the companies, basic styling, and a few lines of copy. There was plenty left to do, from adding search and filtering, to finding more job sources, to adding a database and a real backend. But the minimum product was there. It displayed a list of _good_ jobs.

I posted a [link to the site on HackerNews](https://news.ycombinator.com/item?id=26858935), not expecting much and feeling a bit guilty for even doing that. Surprisingly, the post went to the front page, and brought about 6000 page views over the course of the day. There was plenty of criticism, lots of comments about other sites that did _more_ or did _better_, and a decent amount of discussion about features that were missing. But overall, the feedback was positive. Even though it felt terribly barebones to me, people on HN were seemingly finding value out of my list.

I've heard plenty of others repeat the idea that you should be embarrassed by your V1, but it's one thing to read about it and another thing to experience it. Turns out, it's pretty good advice.

## A few mistakes

Towards the end of the day, I signed up with MailerLite and added a link to sign up for [a mailing list](https://landing.mailerlite.com/webforms/landing/g8q6s7) at the top of the GoodJobs site. I really should have had this on there from the beginning, but 1) I didn't think about it, and 2) I wasn't super sure anyone would sign up for it anyway. Surprisingly, about 25 people did. Since there is no monetization at this point, page views, direct feedback (via email) and mailing list signups are the indicators of success I'm working with.

The overarching mistake here was not having a clear idea of what my metric for success would be from the outset. Obviously, matching someone with a company would be the ultimate success metric, but hard to measure. I thought that getting some email feedback would be cool (and it was!). But, having something more permanent is a better metric. Having a way to keep engaging with engineers who liked the idea and really want to find a job with one of these companies is better than page-views or one-off feedback.

Another mistake here was getting really focused on all the HN traffic. It was exciting to see hundreds of visitors per hour, but when the traffic inevitably died down it felt a bit demoralizing. Would that be it? One day of seeming success, and then nothing? Again, I should've had better criteria for success going in.

## Life after HN

After the HN post, I've had pretty good success with posting GoodJobs in some of the relevant subreddits I'm a part of. None of those posts have brought the traffic that the front page of HN did, but they have kept the discussion alive, and brought the total page views for the month to around 10k. I've also been able to grow the mailing list to about 50 subscribers, which is pretty cool (even though it's a pretty small internet number, it's a pretty big real life number. Imagine 50 people in a room with you!)

I've intentionally tried to spend a lot of time on the 'marketing' side of things with GoodJobs. I have plenty of ideas of how to improve the site, and I've implemented a few of them, while trying to keep to the spirit of 'minimum'. A short list:

- added a 'remote' filter
- added blurbs about the company to each listing
- moved from that JSON file to a [Google Sheet](https://docs.google.com/spreadsheets/d/1Age2wYbTXQubhXkCqHvhZV364ZDvhzUKCud5eKU3ahg/edit#gid=0)(the site now redeploys when I make changes to that sheet -- we'll see how far that scales, but it works for now)

There are lots more improvements I'll make to the site, but I'm also enjoying the choice to work on things outside of my comfort zone (this #buildinpublic style post is another example).

## Closing thoughts

I've titled this post 'One month of building GoodJobs', and in the past I would have struggled with that description. In some ways, I haven't spent that much time building, if we define it as writing code and adding features. But this month has been a learning opportunity for me. I've realized that there's a lot more to talk about when we talk about 'building'. It's hard, and a bit uncomfortable -- and it's also pretty cool.

I'd definitely love to hear your thoughts on this post, or feedback on GoodJobs. Send me something at [tindleaj@gmail.com](mailto:tindleaj@gmail.com)
