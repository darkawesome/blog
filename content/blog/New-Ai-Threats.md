---
title: "New Ai Threats"

date: 2025-11-16T11:19:10-05:00
url: /New-Ai-Threats/
image: /images/2020-thumbs/New-Ai-Threats.jpg

draft: true
---

Following the alleged Chinese AI-cyber campaign we have reached an inflection where cyber campaigns can be enacted by AI.
<!--more-->

- Setting up guardrails around the research labs that create these models is enough to stop the un-initiated but not the resourceful
- following the alleged Chinese AI-cyber campaign we have reached an inflection where cyber campaigns through a 
- Decentralized networks and those using them don't require everyone to be knowledgeable but a single member or a small number and sharing that network access to others

- The barrier for learning how to do something online is ever being lowered as the vast amount of data online is being increased every day
	- OSINT attacks are more viable with the more people are online
		- OSINT information will give you a good basis of information from where a person lives, associated phone numbers, email addresses, social media accounts, where they work and more depending on their online footprint
	- "YouTube University"
		- collection of many IT concepts explained easily online 
	- Colleges/Universities freely sharing class information
		- Universities while moving away from this have and some still do with work arounds share and host lectures, homework, class notes, class lectures online and slides freely
		
- While the guardrails in place by companies like Claude are somewhat effective in stopping some people others have been able to get around them 
	- send emails to “media and law-enforcement figures” with warnings about the potential wrongdoing.
	- alleged chinese threat actor got around guardrails by saying they worked at a cyber security company and only prompting specific parts of the code while not revealing their entire purpose 
	- can just as easily ask another LLM to do what you want as the rules and implementation are not the same across the board and then return to using Claude
	
- Though something more interesting than getting around them is changing lanes completely and self-hosting a model to run on 
	- what guardrails exist in this context to stop a person from utilizing their knowledge or even the collective knowledge that the internet provides and enacting a cyber campaign of their own. 
	- I have an enterprise server capable of 1536GB of total RAM, Up to 7 x PCIe 3.0 plus, and 2 Intel Xeon E5-2650v3 Ten Core 2.3Ghz Processors that currently have 2 NVIDA Quadro k1200' s
		- while bottlenecked by the ram on my graphics cards if i were to utilize 3billion parameter models distilled from larger versions I could instead leverage CPU-based inference using the massive 1.5TB system RAM
			- Once the model has been developed the resource requirements are not the same to run the models as they are to train them. The levels of compute are drastically different. I would argue that if i invested in something in the realm of a NVIDIA RTX A4000 or RTX 4000 SFF Ada which cost in the thousands and not something a graduate student is wanting to fork over. but a drop in the bucket for others
				- RTX 4000 SFF Ada newegg has costs from $500 - 4k
				- NVIDIA RTX A4000 newegg has costs from $1200 - 1400
			- would i be able to run a 400+ Billion parameter model? no, not a chance. However, I could do - Run 13B models very comfortably or run quantized 70B models at usable speeds. And someone of means could look at the bigger model variants and utilize them like what is available for llama, deepseek, qwen3, or gpt-oss
				- GeeksforGeeks
					- "Quantization is a model optimization technique that reduces the precision of numerical values such as weights and activations in models to make them faster and more efficient. It helps lower memory usage, model size, and computational cost while maintaining almost the same level of accuracy."
				- Or in other words dropping the video resolution down from 1080p to 720p 
			-
		- While I wouldn't conduct a cyber campaign with what I have. The cost of the hardware is relatively cheap compared to what a person or group with means could assemble 
		- Running the models on the machine is fairly trivial with programs like ollama and creating agents is made even easier with model context protocol (mcp)
			- Utilizing n8n to create a workflow could automate the entire process to be something where information is dumped into the workflow and the actual reconnaissance, vulnerability discovery, exploitation, and exfiltration operations can be done autonomously
			- information being IP addresses, companies themselves with peoples information discovered by means of OSINT etc etc...
			- This workflow is how I believe that the attack was actually done utilizing the mcp. And the claude agent was just the conductor through the attack
		- **Why didn't they run scripts and automate them like using metasploit run of the mill stuff.**
		- **the exploits were run-of-the-mill so wouldn't defenses against this be the same basic implementation**
		- **Using AI lets you hack more orgs at a time beyond that idk?? Unless the automation expedites the depth of attack and leaves them duped in less than minutes.** 
			- Marcus Hutchins
	- Cloud services could be utilized but for a nefarious actor this type of workload where a firm is attacked would be easy to trace back to an end-user and not make this attack surface viable
- Was this something that they did on purpose for publicity or defamation sort of thing so Claude looks bad?
- 
- I think this begs the question whether the amount that companies spend on cybersecurity enough or even looking at the size of cybersecurity teams. While this is something cool that was done. This doesn't look like a novel idea on its head to present something that couldn't have been done through the use of scripts. The novel Ai part here using mcp could be used for expediting the depth within the network.
- As we go forward in the wargaming and threat assessment landscape the availability of resources available only get cheaper as the technology gets better. The headlines will shock and scare the uninitiated in the dark but not those with a flashlight.





