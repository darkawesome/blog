---
title: "N8n"

date: 2025-12-30T16:00:13-05:00
url: /n8n/
image: /images/2020-thumbs/n8n.jpg

draft: true
---

N8n - Cyber Events Trends Agent
<!--more-->

The following are my notes from an old dataset from US cyber events from UMDs CISSM.

This isn't as professional as I would want this post but I just wanted to document it rather than sit on it. I plan to do more later on as I do more serious work with n8n.

- US centered 
	- Focus on the markers 
- Original approach
	- 6,680 individual items → each with full JSON → sent to LLM
	- this didn't work but a handful of times
		- stopped running from taking >20 mins
		- not finishing the run bc token size was 1.5million
		- the constraints of a laptop
		- the data set was not set up to be read by an LLM 
		- the llm I was using was not set up for such a large context
		- the ouput token usage wasn't limited neither
	- Batch processing 
		- x50 for the LLM to analyze and then combine the results
	- lowered the context size to 2048 to fit the small output 
- New approach
	- basically summarize all of the stuff going into the LLM then have it look for trends
	- offloads some of the math work
		- "Criminal: 90.5% (6,043 incidents)" ← one line instead of 6,043 items - "Healthcare: 22.8% (1,525 incidents)" ← one line instead of 1,525 items 
	- increased the context size a bunch
	- raised the timeout from 5mins to 30minutes


Qwen 2.5:3B
Comprehensive Trend Analysis

1. Temporal Trends - How have incidents changed year-over-year?

The data shows a significant increase in cyber security incidents starting from 2016, reaching its peak in 2020 and then declining sharply in 2023 and 2024.

    Yearly Growth Trends:


        2015: A rise of 34.3% compared to the previous year (2014). This increase suggests a growing awareness or reporting mechanism.

        2016: An additional jump by 20.0%, indicating higher detection and reporting due to technological advancements, increased security awareness, and possibly new attack vectors.

        2017: A decline of 19.7%, which could be attributed to improved cybersecurity measures or a reduction in the effectiveness of some types of attacks.

        2018: Another surge of 23.0% suggests that new vulnerabilities were exploited, possibly due to an increase in targeted attacks on specific sectors (e.g., financial services).

        2019: A significant 34.0% increase shows the resilience of cyber threats against robust security measures.

        2020: An extraordinary rise of 56.9%, likely driven by major data breaches, such as SolarWinds or Marriott International incidents, which highlighted the vulnerabilities in supply chain systems and financial institutions.


    2021: A decrease of 23.3%, showing a reduction in cyber attacks possibly due to an increase in defensive measures (e.g., enhanced security protocols, increased cybersecurity investment).


    2022: An uptick of 32.9% indicates that the pandemic and remote work environment may have created new vectors for attackers, such as ransomware targeting healthcare systems.


    2023: A sharp decline of 40.0%, suggesting a possible decrease in attacks due to heightened awareness, more stringent security measures implemented by businesses and organizations.


    2024: The number drops significantly to only 154 incidents, indicating either a new phase where fewer attacks are being made public or a significant reduction overall.


2. Industry Targeting - Which industries are most affected and why?

The top industries with the highest incident rates are:


    Health Care and Social Assistance (HC&SA): 22.8% of all incidents, indicating that healthcare systems remain highly vulnerable.

    Educational Services: 13.4%, suggesting that educational institutions may lack adequate cybersecurity measures or have become targets for financial motives.

    Public Administration: 12.8%, showing the importance of government security in protecting its critical infrastructure and sensitive data.


These industries are likely targeted due to:


    Sensitivity of Sensitive Data: Healthcare, finance, and public administration sectors handle a significant amount of private information that could be exploited for financial gain.

    Complex IT Infrastructure: These sectors often rely on complex systems with multiple interconnected components, which can provide numerous entry points for attackers.


3. Attack Type Evolution - How have attack methods evolved?

The most prevalent types of attacks are:


    Exploitation of Application Server (38.3%): This indicates a persistent issue in securing application servers against vulnerabilities.

    Data Attack (19.9%): A significant threat where attackers exploit confidential data for financial gain or other motives.


Other notable attack methods include:


    Undetermined (6.2%): These attacks are not clearly defined, suggesting they may involve new and evolving threats that current defense mechanisms have yet to address.

    Message Manipulation (6.1%): This could indicate the rise of social engineering tactics where attackers manipulate messages to extract sensitive information.


4. Actor Patterns - What are the dominant threat actor types?

The key threat actors identified are:


    Criminal Actors (90.5%): These groups continue to dominate cybercrime, driven by financial motivations.

    Hacktivist Groups (3.8%): Although present, they account for a small percentage of incidents and could be targeted efforts from security organizations focused on monitoring these activities.


5. Key Insights and Predictions for Future Trends

## Insights:

    The decline in reported incidents in 2024 suggests potential improvements in cybersecurity measures or decreased incidence rates. However, this does not negate the possibility of a resurgence.

    Healthcare remains a high-risk sector due to its essential role in society and reliance on technology.


Predictions for Future Trends:

    Increased Focus on Emerging Threats: With new technologies emerging (e.g., IoT devices), there will be an increased need to secure these systems against potential vulnerabilities.

    Remote Work Continues to Fuel Attacks: As remote work remains a necessity, organizations must continue investing in robust cybersecurity solutions that protect employees working from home.

    Increased Sophistication of Attack Methods: Criminal groups will likely adopt more sophisticated tactics, including advanced social engineering and zero-day exploits, necessitating continuous improvement in detection systems.

## Conclusion

To mitigate the future threats, governments, businesses, and organizations must invest heavily in proactive cybersecurity measures such as regular software updates, employee training, and robust incident response plans. Additionally, fostering collaboration between public and private sectors will be crucial for effective threat mitigation and prevention of cyber attacks.

## Gemma 3:1b (110 output)

Okay, here's a comprehensive trend analysis based on the provided incident data, focusing on actionable insights and notable patterns:

**1. Temporal Trends (Year-over-Year)**
**Increased Frequency & Severity:** The most striking trend is a significant increase in the number of incidents starting in April 2014.  The initial incident in April is a relatively small disruption, but subsequent incidents escalate in severity and frequency.  This suggests a deliberate, albeit relatively low-level, campaign.

**April as a Spike:** April 2014 appears to be the peak point for these attacks.  The incident itself is a notable outlier, demonstrating a targeted, and somewhat sophisticated, attack.

*Year-on-Year Growth:** While not a linear increase, the number of incidents has increased *slightly* year-over-year since the initial event, indicating a sustained, though potentially less intense, campaign.  A more precise analysis would require historical data.

2. Industry Targeting
3. **“Other Services (except Public Administration)”:**
This is the *most* significant industry target.  The GreaT Team’s focus on Kali's mailing list subdomain suggests a deliberate targeting of services that rely on secure communication channels.  This points to a potential interest in:
* **Software & Security:** Kali Linux is a popular tool for security professionals and developers.  The attack could be aimed at disrupting their workflows or spreading malware.
* **Open-Source Software:**  Kali Linux is a cornerstone of many security training and penetration testing efforts.
* **Digital Security:**  The targeted subdomain suggests a desire to interfere with information sharing related to digital security practices.
* **Limited Targeting:**  While the industry is broad, there's little evidence of targeting specific sectors. This points to a potentially broader, less-specific, or more opportunistic attack.
**3. Attack Type Evolution**
* **Message Manipulation (Disruptive):** This is the core attack type.  The GreaT Team is deliberately altering the content of a mailing list subdomain. This is a classic tactic for:
 * **Spreading Malware:**  Malicious code could be injected into the subdomain to infect systems.
* **Distraction & Confusion:** The attack aims to disrupt communication, potentially causing confusion or leading to data breaches.
* **Reputation Damage:**  A successful attack can damage Kali's reputation as a security tool.
* **Sophisticated (Potentially):** The deliberate targeting of a specific subdomain indicates a degree of planning and sophistication.  It’s not a simple, random attack.
* **Lack of Exploitation:** The attack doesn't exploit known vulnerabilities.  It’s a deliberate manipulation of the existing structure.
***4. Actor Patterns**
**Hobbyist Threat Actor:** This is crucial. The \"Kali\" organization and the \"The GreaT Team\" designation strongly suggest a hobbyist group operating with relatively limited resources.  This is a common tactic for: 
* **Testing & Probing:**  Hobbyists may be experimenting with different attack methods to understand vulnerabilities.
* **Low-Cost Attacks:**  They might be aiming for a smaller, but impactful disruption.

**Targeted & Intentional:** The attack is clearly targeted – it’s not a random event.  The deliberate manipulation of the mailing list subdomain indicates a specific goal.

**Limited Skillset:** The activity suggests a relatively basic understanding of hacking techniques, likely focused on message manipulation.\n\n**5. Key Insights & Predictions for Future Trends**

**Increased Sophistication:**  The trend of increasingly targeted and sophisticated attacks suggests a potential escalation in the threat landscape.  We can anticipate attacks becoming more nuanced and difficult to detect.

**Exploit-Based Attacks:**  While this is a message manipulation attack, it’s *possible* the GreaT Team is attempting to identify and exploit weaknesses in Kali’s infrastructure or security practices.  Further investigation into Kali's security practices is warranted.

**Ransomware Integration (Potential):** While not explicitly stated, the disruption of communication could be a precursor to a larger ransomware campaign.  This is a less likely scenario, but worth considering.

**Exploiting Known Vulnerabilities (Secondary):**  The attack could be a reconnaissance step, attempting to identify systems or services that are vulnerable to exploitation.

**Community-Driven Attacks:**  The hobbyist nature of the group suggests a potential for future attacks to be influenced by community-driven vulnerabilities or exploits.

**Focus on Specific Services:**  We should continue to monitor Kali's ecosystem and identify potential vulnerabilities within specific services that rely on secure communication.

**Actionable Recommendations:**
**Security Audits:** Conduct a thorough security audit of Kali's infrastructure to identify potential weaknesses.
* **Monitoring & Threat Intelligence:**  Increase monitoring of Kali's network and related services for anomalous activity.
* **Incident Response Planning:**  Develop a detailed incident response plan to address potential attacks.
* **Vulnerability Management:**  Prioritize vulnerability patching to address any identified weaknesses.
* **Collaboration:**  Share threat intelligence with other security communities to help identify and mitigate similar attacks.
* To help me refine this analysis further, could you tell me:
* Are there any specific security tools or technologies that you'd like me to focus on in terms of potential vulnerabilities?
* Are there any particular aspects of the incident that you’d like me to elaborate on (e.g., the specific message manipulation technique)?"




