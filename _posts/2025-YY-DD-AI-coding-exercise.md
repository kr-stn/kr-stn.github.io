---
published: false
layout: post
title: KOMrade - an AI coding exercise
---

I was never one to understand *hype* in technology, *excitement* yes, but never *hype*. For technology to truly excite me it first and foremost needs to solve real problems that affect or excite me. For most of the current AI wave, i.e. transformer based (V)LLMs trained on essentially the whole internet, that was only tangentially the case. It was helpful to summarize some texts for me or be a writing sparring partner when I needed new acronyms or needed a starting point to then fully re-write. In that sense it was helpful, like clippy was, but not *exciting*. The current wave of models (Claude Sonnet 3.5 and 3.7, Gemini Flash/Pro 2.0, GPT-4o) however appear to have achieved a level of prediction power that they can be useful in prototyping - and that does start to tickle my excitement. Ever since I transitioned in my job to become an Engineering Manager my hands-on coding skills have, expectedly, atrophied somewhat but my excitement for seeing machines solve problems hasn't.

## Problem Statement (TODO reword)

In my free time I enjoy cycling - alot. I love being outdoors, I love the connections you make when out riding with people and I do enjoy the personal challenges of it. Cycling has a vast set of tools to analyze and improve once's performance and compare yourself with others - something I always liked using as a data nerd even if there is little actual performance to analyze and improve. Strava is the king-of-the-hill for a social, cycling based competing platform with their KOM (King of the Mountain) feature where you can virtually race against each other. I enjoy the challenge of racing for a KOM - it provides an impulse to explore new areas, sets a nice goal and motivation for a training impulse. However, most segments in my city are so fast that they are way out of my abilities to even crack into the Top 10. So whenever I am looking for a new segment to target I spend hours browsing Strava looking for the more niche ones, the ones with the slower riders, the obscure locations. While it is fun to explore the map, virtually and physically, I thought to myself *there has to be a better way*!

## Get help from your KOMrade

Enter KOMrade - your helpful partner in finding achievable KOMs. The idea is simple - provide a map and list of Strava KOMs in a region and provide filters to discover the ones that are achievable. I want it to run in my browser and it should be able to take data from the Strava API as well as from my local machine for power data and as backup.

First note of AI help - coming up with a name that references Strava KOMs, that it is a companion tool, that the German word for bicycle is "Rad" and that ideally is a pun. GPT-4o came up with *KOMrade* - which is neat, but not yet really exciting as a use case to burn through all that electricity.

## Prompt History

To track my own learning process and how the prototype progressed I am keeping the complete prompt history here. The AI and human changes will be tracked as separate git commits in the KOMrade repo. I am (still) fairly handy with Python and know my way around some Javascript - for the rest we are going to use the current set of AI coding assistants and see what prototype we can come up with.

1. The initial application prompt

> I want to build a new browser based application to filter and analyze Strava KOM segments.
>
> Don't write any code yet! Create a plan and an outline of how you want to build this application for me to review.
>
> I should be able to maintain the codebase - I am an experienced Python programmer that knows some Javascript. If the application is purely in Javascript it should be using modern, easy to learn concepts and frameworks.
>
> The application should follow modern web standards and run fully inside the browser locally, without a separate backend.
>
> The workflow of the application is as follows. The user provides a region of interest on a map or as an adress to search for KOMs within a user defined radius. The application then queries the public Strava API or parses the data from the published KOM websites the user can later filter over. The user then filters over the list of available KOMs, with the most interesting categories being length of the segment, elevation, speed, duration, known power of the fastest attempt and number of attempts or people who have ridden that segment. The step of creating a list of available Strava KOMs might take very long so we want to be able to export those results for offline storage and be able to later import them from a local file to speed this step up. In a next step the user can filter over these categories and is presented a list of the filtered segments that are also displayed on a map. Clicking on the segment on the map or in the filtered list should take me to the Strava site of the segment itself.
>
> I know the public Strava API is limited in its scope for what it provides for the KOM segment data. We should aim to use the API minimally - for instance searching by location to get the segment IDs and then parsing the leaderboard and segment data from the public KOM segment pages. This can take multiple minutes or even hours, where the duration and query state should be visible to the user. The results need to be able to be exported after getting them to local storage and the user needs to be able to skip the search and parsing step by re-importing that data.
- since the project plan and outline look reasonable for a JS novice I ask it to start creating the intial code by removen "Don't write any code yet!" and replacing it with "Create a plan and an outline of how you want to build this application and then start building it."

2. Get some help to get it to run locally.
> Add required files and configurations so I can run this application locally using containers or DevContainer. I am on a Windows machine and have WSL2 Linux and Docker available as tools.
- at this point the devcontainers are not launching as expected and a gitignore file is missing, which I had to add manually
