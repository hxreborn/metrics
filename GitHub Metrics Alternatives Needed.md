

# **Architectural Pivot: A Due Diligence Report on Resilient Alternatives for GitHub Profile Metrics Post-API Change**

## **I. Executive Summary: Mitigating API Drift Risk in Profile Automation**

### **1.1. Contextual Confirmation: The API Drift Crisis**

The lowlighter/metrics GitHub action has experienced a critical functionality failure specifically within its recently-used activity tracking component. This disruption is directly attributable to significant GitHub API changes implemented in August 2025\.1 Analysis of the GitHub Availability Report confirms that during August 2025, several incidents occurred, including a production database migration that affected pull request functionality and caused elevated error rates across the REST API traffic, peaking at approximately 4%.1 Such changes, particularly those involving internal database schema alterations (like dropping a column), frequently result in breaking changes for complex, third-party API consumers like lowlighter/metrics.

The subsequent failure to address this specific vulnerability stems from the confirmed maintenance inactivity surrounding the official lowlighter/metrics repository. While the project is extensive, offering 47 plugins and 335 configuration options 2, the lack of active maintainer engagement to resolve critical API dependency issues has rendered the platform unreliable.3 Given this confirmed state of dormancy coupled with a non-fixable API dependency break, relying on configuration workarounds is insufficient. A strategic architectural pivot is required.

### **1.2. Strategic Recommendation: Embracing the Modular Action Strategy**

The fundamental recommendation is the immediate abandonment of the monolithic lowlighter/metrics structure in favor of a **Modular Action Strategy**. This involves replacing the single, feature-rich action with a curated ecosystem of specialized, actively maintained GitHub Actions. This approach distributes risk, minimizes the blast radius of future API failures, and ensures long-term operational resilience.

The inherent failure of lowlighter/metrics was a systemic risk directly tied to its monolithic nature as a complex API consumer. A project featuring dozens of integrated plugins necessitates intricate and deep API queries, likely involving advanced GraphQL or multiple sequential REST calls.2 When GitHub initiated a backend schema change, these complex queries were significantly more likely to encounter corruption or fatal validation errors compared to simpler, focused requests. By shifting to specialized, smaller actions, the system relies on simpler, more stable API endpoints, which offers superior mitigation against future API drift.

### **1.3. Primary Replacement Stack and Key Findings Summary**

The transition necessitates running two primary, independent actions within the GitHub Actions workflow to achieve functional parity with the original tool's most critical features:

1. **Core Stats (Aggregate Metrics):** Utilizing anuraghazra/github-readme-stats (GRS) 5 for general statistics such as total commits, stars, languages, and contribution grade rank.  
2. **Dynamic Activity (Recency Feature):** Utilizing a dedicated activity tracker, such as the widely supported GitHub Activity in Readme (e.g., maintained by JamesIves) 6, to replace the specific, broken "recently-used" functionality.

While this modular configuration increases the perceived configuration complexity—as it requires managing multiple action steps and their outputs, unlike the centralized configuration of lowlighter/metrics 8—it is an acceptable cost for a technical user prioritizing execution stability and long-term security. The analysis confirms that both primary alternatives exhibit active maintenance and have successfully weathered the API turbulence of late 2025\.7

## **II. Root Cause Analysis: The GitHub API Change Incident (August 2025\)**

### **2.1. The Systemic Failure of Monolithic Data Consumption**

The functional breakdown of the lowlighter/metrics recently-used plugin is traceable to the platform incidents GitHub disclosed in its August 2025 availability report.1 Specifically, on August 5, 2025, GitHub initiated a database migration to drop a column backing pull request functionality. Although the column was internally retired, the object-relational mapping (ORM) layer continued to reference this dropped column in a subset of pull request queries. This resulted in elevated error rates across core GitHub services, including webhooks, notifications, and, critically, REST API traffic, peaking near 4%.1

Although GitHub rapidly deployed a fix instructing the ORM to ignore the removed column, the incident revealed inherent instabilities in how changes propagate through the internal infrastructure.1 Complex GitHub Actions, such as lowlighter/metrics, which offers vast feature customization across user activity and repository analysis 2, depend heavily on reliable, high-fidelity responses from the GitHub API. The instability caused by the database migration likely corrupted or altered the schema of the event data stream utilized by the metrics tool.

### **2.2. Vulnerability and Maintenance Deficit of lowlighter/metrics**

The recently-used feature explicitly depends on analyzing recent activity events.2 This necessitates consistent access to the user event stream (often accessed via the REST API /users/:user/events or a specialized GraphQL query structure). The core causal link between the August 2025 incident and the tool’s failure is highly probable: the internal GitHub database change affecting query responses 1 likely resulted in malformed or incomplete event data being returned to the metrics parser. This would have caused the recently-used plugin's parsing logic to encounter fatal schema validation errors, rendering the feature inoperable.

This systemic failure underscores the inherent risk of tightly coupled, highly complex integrations with large API platforms that do not strictly enforce long-term version stability. If metrics relied on non-versioned or implicitly deprecated fields, its fate was sealed when GitHub optimized its backend schema. The architectural complexity of the tool, with its 47 plugins, demanded constant vigilance to maintain compatibility across all API dependencies.2 This requirement proved unsustainable.

Further analysis confirms that migration is non-negotiable due to project status. The lowlighter/metrics issue tracker shows issues regarding maintenance inactivity, including a stale bot aggressively closing pull requests.3 While project maintainers discussed plans for v4 development in late 2023 3, the lack of a quick patch for a core API dependency issue confirms the dormancy of critical maintenance operations. The large number of features provided by the tool became a liability, as maintaining compatibility for nearly fifty dependencies across evolving GitHub APIs is highly resource-intensive, which explains the difficulty in quickly issuing a fix for a single broken plugin.

## **III. Strategic Migration Framework: Monolith vs. Modular Actions**

### **3.1. Defining Functional Parity Requirements**

To successfully replace the functionality of lowlighter/metrics, the replacement stack must meet three distinct levels of functional parity:

1. **Level 1 (Core Stats):** Requires dynamically generated aggregate contribution data, including total commits, PRs, stars, issues, and a contribution grade ranking.5  
2. **Level 2 (Dynamic Recency):** Requires a chronological list of recent activity events (the exact function lost in the August 2025 failure).2  
3. **Level 3 (Aesthetics & Niche):** Requires visually appealing contribution statistics and the capacity to integrate external data streams (e.g., WakaTime, StackOverflow, or visual elements like the 3D Skyline).10

### **3.2. Rationale for the Modular Strategy**

The modular approach addresses the failure mode of the monolithic architecture. By utilizing independent, single-purpose GitHub Actions, the system gains superior agility in responding to API changes. Because each action maintains a smaller, more focused codebase, hotfixes for API dependency issues can be implemented and deployed faster, as evidenced by the post-August 2025 activity of the recommended alternatives.7 The risk exposure is decentralized; if one specialized tool (e.g., the WakaTime integration) fails, the core stats and recent activity modules remain operational.

The necessary trade-off for this enhanced stability is an increase in configuration effort. Instead of using a single YAML configuration file, the modular approach requires defining multiple workflow steps within the GitHub Actions runner. These steps will execute components like anuraghazra/github-readme-stats, a dedicated activity tracker, and potentially aesthetic generators like Platane/snk. This cost, however, is acceptable for a developer persona who prioritizes long-term reliability and distributed maintenance over configuration convenience.5

### **3.3. Modular Feature Mapping and Replacement Strategy**

The transition necessitates a systematic mapping of the critical features provided by lowlighter/metrics to the most robust and actively maintained alternatives.

Modular Feature Mapping and Replacement Strategy

| lowlighter/metrics Feature | Functionality Provided | Recommended Modular Replacement Tool | Reasoning |
| :---- | :---- | :---- | :---- |
| plugin\_activity / recently-used (Broken) | Dynamic list of recent GitHub events (Pushes, Stars, Forks) | GitHub Activity in Readme (e.g., JamesIves) 6 | Direct, actively maintained replacement confirmed operational post-August 2025 API instability.7 |
| base / General Statistics | Total Stars, Commits, PRs, Issues (Grade Rank) | anuraghazra/github-readme-stats (Stats Card) 5 | High adoption, stable core statistics engine, and simple implementation via URL or self-host.12 |
| plugin\_languages | Programming language breakdown/colors | anuraghazra/github-readme-stats (Languages Card) 9 | Provides efficient, customizable language calculation based on repository usage. |
| Niche Plugin (e.g., WakaTime) | Weekly coding metrics/time tracking visualization | Dedicated WakaTime action (Dev Metrics in Readme) 13 | Reduces dependency risk by outsourcing external API integration to a specialized action. |
| Niche Plugin (e.g., Skyline 3D) | Aesthetic visualization of contributions | Platane/snk (Snake Game Generator) \[11\] | Provides a unique, actively maintained visual element replacing the contribution calendar/skyline functionality. |

This migration strategy shifts the integration layer from the internal logic of a single action to the structure of the README.md itself. Where lowlighter/metrics generated a single, consolidated SVG image 14, the modular approach requires embedding multiple images (github-stats.svg, activity.md, snake.gif, etc.) using appropriate Markdown or HTML embedding tags (e.g., \<img align="center"...\> 8).

Running multiple specialized actions in concert, often scheduled on a daily cron job 8, necessarily increases workflow time and resource consumption. This management of workflow latency is a critical consideration. While this increase is minimal for typical daily updates, it necessitates optimizing scheduling and reviewing the frequency of resource-intensive features (like the PageSpeed checks included in the original metrics suite 2), which may need to be run less frequently than the core metrics or even deployed on a separate infrastructure.

## **IV. Alternative I: Core Metrics Generation \- The GitHub Readme Stats (GRS) Ecosystem**

### **4.1. Project Health and API Stability Assessment**

The anuraghazra/github-readme-stats (GRS) project stands as the most robust alternative for core, aggregated profile statistics. Its project health is superior, confirmed by recent commit activity occurring "last week".9 This high velocity of maintenance post-August 2025 is a strong indicator of GRS’s ability to manage and rapidly respond to modifications in GitHub’s API infrastructure, contrasting sharply with the dormancy of lowlighter/metrics.

### **4.2. Feature Parity Analysis (Aggregate Data)**

GRS excels at Level 1 (Core Stats) parity. Its foundational output is the GitHub Stats Card, which displays aggregate metrics such as star count, total commits, pull requests, and issues, often accompanied by a calculated grade rank for overall contributions.5 It also provides the Top Languages Card, which calculates and displays the programming language breakdown across repositories.9

GRS offers a robust suite of customization options via URL parameters, including the ability to hide specific aggregate statistics (e.g., \&hide=stars), choose from various aesthetic themes, and adjust locale settings.9

It is essential to understand the functional limitation of GRS in this migration context: GRS is primarily designed for *aggregated totals* and overall ranking. It does *not* provide the granular, chronological listing of recent activity events (i.e., the specific push or star actions) that the user requires to replace the defunct lowlighter/metrics recently-used feature.9 This limitation confirms the necessity of integrating a dedicated, second solution (Alternative II).

### **4.3. Implementation Strategy: Mitigating Rate Limits and Private Data Gaps**

While GRS offers the simplest implementation method—direct embedding using a public URL—this approach carries two significant drawbacks: rate limiting, and the inability to access private repository data.12 Since the public GRS instance uses a shared GitHub API token, high traffic volume can lead to rate limiting, causing the card to fail to load.12 Furthermore, by design, the default public instance can only display statistics derived from public contributions.9

To achieve the comprehensive data access equivalent to the full feature set of the original metrics action, self-hosting is the mandatory best practice. Deploying a personal instance of GRS on platforms like Leapcell or Vercel, or leveraging the GitHub Action approach, allows the user to utilize a dedicated Personal Access Token (PAT). This action isolates the system from public rate limits and unlocks the capacity to track private contributions.12

### **4.4. Critical Token Requirements for GRS**

If self-hosting or using the GitHub Action approach for GRS is chosen, a PAT must be created and securely stored as a repository secret. While minimal read access is sufficient for public data, tracking private contributions requires expanded scopes.9 This requirement reinforces the security paradigm: even if the tool is actively maintained, relying on a dedicated PAT requires adherence to security best practices.

## **V. Alternative II: Dynamic Activity Tracking \- Dedicated Solutions**

### **5.1. The Direct Replacement: GitHub Activity in Readme**

The primary objective of this migration is to restore the broken dynamic event listing capability. The ideal candidate for this task is the GitHub Activity in Readme action (e.g., maintained by JamesIves).6

**Activity and Resilience:** This action provides the most conclusive evidence of stability and resilience against the August 2025 API instability. Documentation confirms updates and operation as recently as August 31st, 2025\.7 This operational history proves that the tool successfully navigated the systemic API turbulence that felled lowlighter/metrics.1

**Functionality and Setup:** The core function of this action is to query the user’s recent activity events and commit a formatted chronological list of these events directly back into the profile repository's README.md file.6 This output directly replicates the intended purpose of the broken metrics recently-used plugin.

Implementation requires two key steps:

1. Defining the insertion points within the README.md using specific marker comments, such as \`\`.6  
2. Configuring a GitHub workflow (typically running daily via a cron schedule) to run the action, specifying parameters like the maximum number of lines (MAX\_LINES) and the target file.6

### **5.2. Token Requirements and Least Privilege Principle**

Dedicated activity tracking actions generally require minimal token permissions, especially if they only track public activity, often relying on the default GITHUB\_TOKEN provided within the GitHub Actions environment. However, if the user requires the display of private activity events, a narrowly scoped PAT must be used, consistent with the security principle of least privilege.8

### **5.3. Supplementary Dynamic Metrics and Commit Management**

The simultaneous execution of two separate actions—GRS (for aggregate data) and the Activity Tracker (for granular events)—introduces a necessary management consideration: commit noise. Both actions generate commits daily (if scheduled by cron) to update the README output files (SVG/Markdown).8 If no new activity has occurred, the system must prevent generating useless "empty" commits, which clutter the repository history. The GitHub Activity in Readme action provides mitigation by allowing definition of an EMPTY\_COMMIT\_MSG.6 The user must optimize the scheduling of these actions to respect GitHub's secondary rate limits, even when using a dedicated PAT. While GRS offers cache control (cache\_seconds parameter 9), running both processes should be limited to the minimum necessary frequency (e.g., a daily cron) to ensure API call efficiency and prevent throttling.12

Other supplementary actions can complement the dynamic display, such as the GitHub Streak Stats action, which focuses specifically on displaying the total contribution count, current streak, and longest streak, providing a persistent visualization of recent activity achievements.13

## **VI. Comprehensive Feature and Implementation Comparison**

### **6.1. The Migration Toolkit: Feature-by-Feature Gap Analysis**

The shift to the modular architecture requires managing at least three separate components—Core Stats (GRS), Dynamic Activity (Activity Readme), and Niche Plugins (e.g., SNK or WakaTime)—to achieve the functional parity previously offered by the single lowlighter/metrics workflow.5

The implementation trade-off is quantified in the comparison below, highlighting the superior operational characteristics of the actively maintained modular components over the defunct monolith.

Functional Comparison of Dynamic Profile Tools

| Metric Category | lowlighter/metrics (Original) | GitHub Readme Stats (GRS) | GitHub Activity in Readme (Dedicated) |
| :---- | :---- | :---- | :---- |
| **Core Functionality** | Comprehensive Stats, 47 Plugins (Monolithic) 2 | Standard Stats Card, Language Card (Aggregate Focus) 9 | Updates README with recent user actions (Dynamic Event Focus) 6 |
| **API Resilience Status (Post-Aug 2025\)** | Critically broken/unmaintained.1 | High. Actively maintained (commits "last week").9 | High. Actively maintained (updates confirmed Aug 31, 2025).7 |
| **Token Requirement for Full Data** | Required for almost all plugins/full features (broad scopes needed).8 | Required for private contributions/rate limit avoidance.\[9, 12\] | Required for private activity tracking (minimal scopes preferred). |
| **Setup Complexity** | Moderate (Complex YAML/Plugin configuration).8 | Low (Simple URL parameters or self-host setup via Vercel/Leapcell).12 | Low to Moderate (Requires workflow setup and precise Markdown insertion points).6 |

### **6.2. Advanced Customization Comparison**

The customization methodology differs significantly between the two systems. lowlighter/metrics offered high-level customization directly within its workflow YAML via dedicated options like extras\_css and extras\_js.8 This integrated approach allowed for granular, single-source styling control.

The modular approach decentralizes customization. GRS customization is achieved primarily through URL parameters controlling themes, colors, and included statistics.9 The Activity Readme tool uses configuration parameters like MAX\_LINES to control output quantity.6 Achieving the aesthetic consistency of the original metrics package requires the user to employ custom themes within each modular tool, and then utilize standard Markdown and HTML embedding features to compose the final layout within the README.md.8

## **VII. Future-Proofing and Advanced Customization**

### **7.1. Aesthetics and Visualization**

To compensate for the loss of the visualization plugins (such as the contribution calendar or GitHub Skyline 3D calendar) found in lowlighter/metrics 10, specialized aesthetic generators should be integrated as separate workflow steps.

A prime example is Platane/snk, which generates a compelling animation (GIF or SVG) depicting a snake game based on the user’s contribution graph.11 This provides an actively maintained visual counterpart to the contribution calendar and delivers the aesthetic "wow" factor often associated with dynamic profile visualizations. Tools like GitHub Streak Stats 13 also offer visually engaging displays of long-term consistency.

### **7.2. Integrating External Data Feeds**

The original metrics architecture included numerous niche plugins for external data, such as WakaTime, StackOverflow, and Anilist integration.10 The robust strategy for replacing these is to avoid building monolithic API integrations and instead rely on community-maintained, single-purpose actions specifically designed for those data sources. For instance, WakaTime metrics are best integrated using a dedicated WakaTime action (Dev Metrics in Readme 13), which outsources the fragility of integrating external APIs to specialized maintainers. Similarly, RSS feeds can be integrated using tools like Feedparser 13 coupled with an action that updates the README content.

### **7.3. Enhanced Security Architecture (Principle of Least Privilege)**

The shift to a modular architecture represents a significant security upgrade. The monolithic nature of lowlighter/metrics often necessitated granting a single Personal Access Token (PAT) broad scopes, which could include public\_repo, read:org, repo, read:packages, and gist access.8

The modular stack drastically reduces the security risk by enabling the use of the Principle of Least Privilege. Each action can be configured to use a PAT with only the minimal scopes required for its specific function. For instance, the Activity Tracker may only require public event read access (often achievable with the default GITHUB\_TOKEN), whereas GRS may only need repository read access if private data is required. If one modular action were compromised, the potential blast radius of exposed data or manipulation is significantly lower than that of the original highly privileged monolithic token. Therefore, the best practice is to always rely on the default GITHUB\_TOKEN for public data retrieval and only generate a dedicated, narrowly scoped PAT when private data access is mandatory.

A key consideration in adopting the modular approach is the resulting dependency on multiple independent maintainers (e.g., Anurag Hazra, James Ives, Platane). While this distributes the API failure risk, it increases the number of individual projects whose long-term health must be monitored. For the most critical components, such as GRS, future-proofing can involve proactively forking the repository 8 or utilizing deployment platforms like Leapcell 12 that facilitate quick redeployment options, ensuring local control during future unforeseen GitHub API crises.

## **VIII. Conclusions and Final Recommendations**

The failure of the lowlighter/metrics recently-used plugin following the August 2025 GitHub API incident confirmed the inherent instability of highly complex, monolithic API integrations when faced with infrastructure changes.1 The recommended solution is a strategic architectural migration to a modular stack of specialized, actively maintained GitHub Actions, offering superior resilience and security.

### **8.1. Recommended Migration Path Summary**

The following steps define the technical path for establishing the robust, modular profile metric stack:

1. **Token Review:** Determine if private contribution data is required. If so, generate a new Personal Access Token (PAT) with the absolute minimum required read scopes (public\_repo or read:user) and securely store it as a repository secret (e.g., PAT\_TOKEN).  
2. **Core Stats Implementation (GRS):** Implement anuraghazra/github-readme-stats. If private data or rate limit mitigation is critical, prioritize self-hosting (e.g., via Vercel) or running GRS as a dedicated GitHub Action using the PAT. Otherwise, embed the public URL with required parameters (theme, hide, show 9).  
3. **Dynamic Activity Implementation:** Set up the dedicated activity action, such as GitHub Activity in Readme (JamesIves). Configure the GitHub workflow to run this action, ensuring the correct and markers are placed precisely within the README.md.6  
4. **Workflow Scheduling:** Schedule both the Core Stats action and the Dynamic Activity action to run independently via a daily cron schedule (e.g., "0 0 \* \* \*" 8).  
5. **Aesthetic and Niche Integration:** Integrate supplementary actions like Platane/snk and dedicated WakaTime actions as separate, distinct steps within the primary workflow, utilizing the README as the final integration canvas.

### **8.2. Final Prognosis**

The shift from the single-point-of-failure monolithic structure of lowlighter/metrics to a distributed, modular approach ensures enhanced security, distributed dependency risk, and significantly improved agility in responding to future platform changes. While this transition requires managing multiple components, the resulting stability is an essential architectural upgrade for maintaining a dynamic GitHub profile README in the face of ongoing API evolution.

#### **Obras citadas**

1. GitHub Availability Report: August 2025, fecha de acceso: octubre 31, 2025, [https://github.blog/news-insights/company-news/github-availability-report-august-2025/](https://github.blog/news-insights/company-news/github-availability-report-august-2025/)  
2. lowlighter/metrics: An infographics generator with 30+ plugins and 300+ options to display stats about your GitHub account and render them as SVG, Markdown, PDF or JSON\!, fecha de acceso: octubre 31, 2025, [https://github.com/lowlighter/metrics](https://github.com/lowlighter/metrics)  
3. Issues · lowlighter/metrics \- GitHub, fecha de acceso: octubre 31, 2025, [https://github.com/lowlighter/metrics/issues](https://github.com/lowlighter/metrics/issues)  
4. Yet Another GitHub Profile Generator \- House Absolute(ly Pointless), fecha de acceso: octubre 31, 2025, [https://blog.urth.org/2022/03/28/yet-another-github-profile-generator/](https://blog.urth.org/2022/03/28/yet-another-github-profile-generator/)  
5. GitHub Metrics | Create an Excellent GitHub Profile with Markdown, fecha de acceso: octubre 31, 2025, [https://learn.adafruit.com/excellent-github-profile/github-metrics](https://learn.adafruit.com/excellent-github-profile/github-metrics)  
6. GitHub \- Activity \- Readme · Actions · GitHub Marketplace, fecha de acceso: octubre 31, 2025, [https://github.com/marketplace/actions/github-activity-readme](https://github.com/marketplace/actions/github-activity-readme)  
7. Recent GitHub Activity \- Profile Readme · Actions · GitHub Marketplace, fecha de acceso: octubre 31, 2025, [https://github.com/marketplace/actions/recent-github-activity-profile-readme](https://github.com/marketplace/actions/recent-github-activity-profile-readme)  
8. Upgrade Your GitHub README.md 2.0 | by Tina Huynh \- Medium, fecha de acceso: octubre 31, 2025, [https://medium.com/@tmchuynh/upgrade-your-github-readme-md-2-0-b9d325dd0a0c](https://medium.com/@tmchuynh/upgrade-your-github-readme-md-2-0-b9d325dd0a0c)  
9. anuraghazra/github-readme-stats: :zap: Dynamically ... \- GitHub, fecha de acceso: octubre 31, 2025, [https://github.com/anuraghazra/github-readme-stats](https://github.com/anuraghazra/github-readme-stats)  
10. View github: lowlighter/metrics | OpenText Core SCA \- Debricked, fecha de acceso: octubre 31, 2025, [https://debricked.com/select/package/github-lowlighter/metrics](https://debricked.com/select/package/github-lowlighter/metrics)  
11. Platane/snk: Generates a snake game from a github user contributions graph and output a screen capture as animated svg or gif, fecha de acceso: octubre 31, 2025, [https://github.com/Platane/snk](https://github.com/Platane/snk)  
12. Unleash the Full Power of Your GitHub Readme Stats | by Leapcell | Sep, 2025 \- Medium, fecha de acceso: octubre 31, 2025, [https://leapcell.medium.com/unleash-the-full-power-of-your-github-readme-stats-a794a3a6df19](https://leapcell.medium.com/unleash-the-full-power-of-your-github-readme-stats-a794a3a6df19)  
13. abhisheknaiidu/awesome-github-profile-readme: A curated list of awesome GitHub Profile which updates in real time, fecha de acceso: octubre 31, 2025, [https://github.com/abhisheknaiidu/awesome-github-profile-readme](https://github.com/abhisheknaiidu/awesome-github-profile-readme)  
14. Take your GitHub profile from zero to hero with these 10 hacks | by Kaustav Ganguly | Medium, fecha de acceso: octubre 31, 2025, [https://medium.com/@kaustav\_g/take-your-github-profile-from-zero-to-hero-with-these-10-hacks-b6b5ffe3d065](https://medium.com/@kaustav_g/take-your-github-profile-from-zero-to-hero-with-these-10-hacks-b6b5ffe3d065)  
15. The Card for Github stats has not updated probably since 2023 · Issue \#4111 · anuraghazra/github-readme-stats, fecha de acceso: octubre 31, 2025, [https://github.com/anuraghazra/github-readme-stats/issues/4111](https://github.com/anuraghazra/github-readme-stats/issues/4111)  
16. Platane/snk: Eat Up All Your GitHub Contributions \- HelloGitHub, fecha de acceso: octubre 31, 2025, [https://hellogithub.com/en/repository/Platane/snk](https://hellogithub.com/en/repository/Platane/snk)