---
title: "Homework Assignment #1"
teaching: 0
exercises: 60
questions:
- "What's the homework for the first day?"
objectives:
- "Work with your team on a collaborative repository."
- "Open a pull request."
- "Review someone else's pull request."
keypoints:
- "You've now submitted a pull request and reviewed a pull request on a collaborative repo!"
---

## Homework

For this homework assignment, you will be collaborating with your team on a GitHub pages site. You will update this site with reflections during the course of the bootcamp. Today, everyone should contribute to the site. In the future, you should rotate so that one person each day is writing a reflection or summary on that day's topic. 

For today, you should interview and make a blog post about someone else on your team. Some questions you might think about...

1. Where is your team member from?
1. Where do they live?
1. Do they have any pets?
1. What is your favorite food? What is your least favorite food?
1. What is one thing the two of you have in common (that isn't the MSSE program)?
1. Why did they choose the MSSE degree?

You have been added as a collaborator to a repository. Follow the instructions on the sample blog to make your first update and submit a pull request. Everyone add a blog post.

### Team 1
1. Jasmine
1. Ron
1. Camille  
[GitHub Repository](https://github.com/msse-2020-bootcamp/team1-blog) | [Blog Site](https://msse-2020-bootcamp.github.io/team1-blog/)

### Team 2
1. Thenuka
1. Angela  
[GitHub Repository](https://github.com/msse-2020-bootcamp/team2-blog) | [Blog Site](https://msse-2020-bootcamp.github.io/team2-blog/)

### Team 3
1. Xudong
1. Muhan  
[GitHub Repository](https://github.com/msse-2020-bootcamp/team3-blog) | [Blog Site](https://msse-2020-bootcamp.github.io/team3-blog/)

## Prep for Tomorrow

Tomorrow we will start writing a program to do a simulations of a chemical system. To check our work, we will be comparing the results of our code to benchmarks presented by the National Institute of Standards and Technology (NIST). Please visit [this page](https://www.nist.gov/mml/csd/chemical-informatics-research-group/lennard-jones-fluid-reference-calculations) and download the files linked at `gzipped tarball archive`. Save this file. The file should have a `.tar` extension. If it does not, (on Mac sometimes it seems to download ending in `-tar`), change the file extension then unzip the file. Put the unzipped folder in the directory where you are keeping files for this bootcamp. Make sure the name of the folder is `lj_sample_configurations`.

If you are on Windows in the WSL, you will need to obtain the files using the command line. Navigate to your `msse-bootcamp` folder:

~~~
wget https://www.nist.gov/document/ljsampleconfigurations-targz
tar -xzvf ljsampleconfigurations-targz
~~~
{: .language-bash}
