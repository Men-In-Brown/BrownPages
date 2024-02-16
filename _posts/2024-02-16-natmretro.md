---
toc: True
comments: True
layout: post
title: N@TM Checkpoint
description: Reflections on our part in Night At The Museum, other people's CSA projects, and other classes' projects during the showcase.
type: tangibles
---

# Two Trimester Focus Planning

### Collecting Data from N@TM For Our Application

Although we had good code and a better presentation, we wanted to take the opportunity of N@TM to collect data from random people. We wanted to use this data for finding out what's good about our appication, what could be better, and overall how to proceed. Here's a screenshot of the screen we used to collect data (through our Google form):

![img1](images/Screenshot%20(100).png)

### Putting Feedback To Use

Below is an image showing all the responses to the data we requested from the participants and audience:

![img2](images/Linkr%20Responses.png)

From these responses, we will add many things to our application. Our to-do list (from the responses solely) now looks like this:

- Improve on SASS to make the application more user friendly than it already is. This includes subtle animations, bouncier UX/UI, and the elimination of unnecessary elements.
- Get a better understanding of why users may be reluctant to support a startup like this.
- Make the page less basic. This means removing default styling and creating our own styles, possibly from scratch.
- Get a better understanding of why our current UX/UI isn't as good as it could be.
- Possibly a community page or forum to tag companies, employees, etc.
- Adding column to companies: Stock price. It could update every hour or every day.
- Fix formatting.

### Presentation:

We as high schoolers constantly have great ideas, and sometimes we make something out of them, but a consistent problem with our ideas is that they never go anywhere because they don't get noticed by a lot of people. Our team wanted to solve this problem. Enter: Linkr. We take the swipe-heavy mechanic from popular applications like Tindr, TikTok, Instagram Reels, etc. and the countless company ideas from sites such as LinkedIn and created a website where users can swipe through company startup ideas until the find one that they'd either like to invest in or get employeed in. 

![img3](images/Screenshot%20(105).png)

We shall skip the login and signup process, because it's a pretty basic and standard feature that every website here has. What we'd like to do instead is show you the heart of our website: the Company Findr. Here is where you'll be able to scroll through the countless company startup ideas that users have created.

![img4](images/Screenshot%20(107).png)

The website is pulling data from a database with thousands of company ideas. We made the database ourselves using Java Spring, and the frontend is HTML/CSS/JS. 

When you've swiped enough, you'll probably come across a company that you'll want to either invest or get employeed in. This is where you'll either swipe left or right. Let me swipe left for you to indicate to the side that you'd like to invest in this company.

![img5](images/Screenshot%20(106).png)

I just want to quickly add here that this is a feature that I've never tried before, but that I'm glad I did for this project. What's happening here is that when you swipe, the name and mission of the company (the fields display on the previous screen) are stored in local data. On the new page, the local data is then pulled and displayed on the current page. So technically, the data from the backend is moving through pages of the website. It's a subtle addition to our website, but a valuable one. 

When you find a company you want to invest, it shows the screen above. Later, we plan to add more columns from the company's row in the screen later. For now, it shows the same company name and mission as the previous screen. It has a confirmation checkbox that allows you to understand that you aren't actually investing money into this idea, but you are rather simply showing your support through this "investment." When the investment button is clicked, we plan to add the user logged in to the company row's investment field (we must create that column, too).

For the more technical audience, we'd like to show you our backend. 

![img6](images/Screenshot%20(108).png)

Above, you'll see the backend admin panel. It uses Thymeleaf to grab the same login data from the backend, except now it is all one repository. This allows for a more integrated experience, which can be applied to admin panels as you see here. In this admin panel, when I log in with admin creditials, you can see all the users created within the website. 

And that's our application. If you would like, please do fill out our feedback form [here](https://docs.google.com/forms/d/1-b2zZKDX3sF3nEUhQRYqraPbetOr0hgiP2QHgLld0Do/edit?usp=forms_home&ths=true).

# Our Team

### Glows

I am profoundly grateful for the positive aspects that illuminated our presentation, particularly the strong connection we forged with our audience. One notable highlight was our preparedness to cater to both non-technical and tech-savvy individuals. Fortuitously, our audience leaned towards the latter, enabling us to delve into more advanced explanations and showcase intricate code snippets beyond the ordinary user's perspective. This alignment with the audience's technical prowess allowed us to create a more engaging and insightful presentation.

The rapport we established not only facilitated a smooth flow of information but also enhanced the overall learning experience. The ability to tailor our content to a tech-savvy audience undoubtedly showcased the depth of our knowledge and the robustness of our presentation skills. This connection not only made the session more enjoyable but also highlighted our adaptability and versatility in delivering content.

### Grows

Despite the numerous strengths of our presentation, a reflective analysis also revealed areas where we could enhance our performance, with a focus on improving professionalism. While we navigated the intricacies of our topic admirably, there were instances where we allowed ourselves to become momentarily distracted. As second-year Computer Science students at DNHS, I firmly believe that we can achieve a presentation characterized by seamless focus and unwavering professionalism.

Recognizing these moments of distraction as opportunities for growth, we can implement strategies to bolster our concentration and maintain a consistently polished delivery. Whether through additional practice, implementing mindfulness techniques, or refining our presentation scripts, there are various avenues we can explore to eliminate distractions and elevate our overall professionalism.

By addressing these areas of improvement, we not only refine our current presentation skills but also cultivate habits that will serve us well in future endeavors. Striving for a distraction-free presentation aligns with our commitment to continuous improvement and ensures that our audience receives an experience characterized by unwavering focus and professionalism. As we embark on future presentations, these insights serve as valuable guideposts for refining our approach and enhancing the overall impact of our communication.

# The Event

### A Project in CS

In my exploration of various Computer Science Association (CSA) projects, one that particularly caught my attention was the messaging website crafted by the collaborative efforts of Orlando, Shivansh, Shreyas, and Mati. Their innovative approach to incorporating artificial intelligence (AI) into the platform stood out, showcasing a noteworthy fusion of technology and user experience.

A distinctive feature that captured my admiration was the implementation of an AI-powered chatbot. This ingenious addition served as a thoughtful solution for users encountering situations where direct communication with others might not be readily available. By introducing an intelligent chatbot alternative, the team demonstrated a keen understanding of user needs and a commitment to providing a seamless and accessible messaging experience.

The strategic integration of AI not only highlighted the team's technical prowess but also underscored their ability to think beyond conventional messaging platforms. The chatbot acted as a valuable intermediary, offering users a dynamic and responsive engagement even when their intended recipients were unavailable. This not only enhanced the overall functionality of the website but also showcased a forward-thinking approach to user interaction and convenience.

Furthermore, the collaborative efforts of Orlando, Shivansh, Shreyas, and Mati were evidently fruitful in creating a cohesive and innovative project. The success of their messaging website not only stems from the technological advancements but also from their effective teamwork, where diverse skills and perspectives converged to produce a well-rounded and impactful solution.

In conclusion, the messaging website developed by this team exemplifies the fusion of technology and user-centric design, with the AI-powered chatbot serving as a testament to their creativity and problem-solving acumen. This project stands as an inspiring example within the CSA, demonstrating the potential of leveraging AI to enhance user experiences and redefine the possibilities within the realm of messaging platforms.

### Outside of CS

In photography, I saw a photo that I really liked that was submitted by someone I didn't know. However, the reason I liked it was the story behind it. A few months, I had a friend, Camden, who showed me a picture of him laying down on abandoned train tracks in a clear sky in front of grassy mountains. It was pretty beautiful, and I joked that he should take Photography just to submit that photo. While he didn't do that, he must have given it to his friend, because I saw that very photo in the Photography section of N@TM. It felt like a full circle moment, and re-assured me that life may never run out of fun moments like these.