---
title: "Building a LeetCode Penalty Enforcer"
datePublished: Mon Apr 28 2025 05:25:55 GMT+0000 (Coordinated Universal Time)
cuid: cma0mxnmm000r09kv53cz9fa8
slug: building-a-leetcode-penalty-enforcer
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745259011594/5baa619e-b69e-4844-85f8-047bc056eb80.png
tags: productivity, software-development, programming-blogs, python, automation, software-engineering, habits, accountability, leetcode

---

Final Code: [BosTheCoder/leetcode-accountability: Keep you accountable to coding practice](https://github.com/BosTheCoder/leetcode-accountability)

### [Introduction](https://github.com/BosTheCoder/leetcode-accountability)

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text"><strong>Accountability Group:</strong> a <strong><mark>small group of individuals who come together to support each other in achieving personal or professional goals</mark></strong>. These groups create a sense of shared responsibility and motivation, helping members stay on track and hold themselves accountable for their commitments.</div>
</div>

It wouldn’t be a stretch to say accountability groups changed my life. They've been a cheat code for me building consistent habits and achieving goals by leveraging social pressure and financial incentives.

If I’m being total honest with myself I think I might abuse them slightly😅Right now, I’m in several: one ensures I produce content weekly (or face a £30 fine), another guarantees I wake up by 5 AM every day (£10 fine for failures), and several others for different areas of personal improvement.

Recently, I created another group specifically aimed at solving LeetCode problems daily. The intention behind this was mainly to ace future job interviews, conquer the fear of the [PIP](https://www.teamblind.com/post/Performance-Improvement-PlanPIP-8T6eDDcs) (cough cough Meta employees cough), and ideally, avoid ever needing the dreaded LeetCode grind again. Initially, our group imposed a penalty of £10 for each day missed, but this VERY QUICKLY proved too rigid. So, we adjusted to a more flexible system—solving 7 problems per week, with a £10 fine for each problem missed below that quota.

However, even with good intentions, enforcement became lax over time—we often forgot to apply penalties or were too lenient (constant holidays from our particapting members didn’t help, myself included…)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745254466263/ada8c2bc-8d72-4509-95f7-9531503197f1.gif align="center")

Thus, the idea for automating penalty enforcement via a Python script was born✨.

### Project Requirements

I wanted to get a Minimal Viable Product (MVP) within a single Sunday (or approximately 10 hours of hardcore work). As a result, I tried to limit the scope to these requirements:

* Ability to input LeetCode usernames.
    
* Filter questions solved by date ranges and question types.
    
* Notify via email.
    
* CRITICAL: Integrate with [SplitWise](https://secure.splitwise.com/#/dashboard) to manage penalties.
    

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Splitwise is <strong><mark>a cost-sharing app and website that helps users track and settle shared expenses with friends, family, or group members</mark></strong>. It organizes shared expenses, calculates who owes whom, and allows users to easily pay each other back</div>
</div>

I thought of SplitWise as an appropriate choice for a weekend project like this as it can handle all the splitting of payments with just some simple REST API calls, AND the majority of my friends use it for things like holidays.

### Initial Exploration

The first step was to explore LeetCode's GraphQL API and SplitWise's API. This of course took longer than anticipated because LeetCode’s api lacks ANY sort of documentation and of course doesn’t allow Introspection. This meant exploring it required reverse-engineering it using Chrome's developer tools as well as using copius amounts of ChatGPT 4o to scan object structures from community GitHub repositories. Splitwise also had less-than-ideal documentation, complicating authentication and API usage initially.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text"><a target="_self" rel="noopener noreferrer nofollow" href="https://graphql.org/learn/introspection/" style="pointer-events: none">Introspection</a> in GraphQL is a super useful tool for exploring endpoints. For security purposes it is <a target="_self" rel="noopener noreferrer nofollow" href="https://www.apollographql.com/blog/graphql/security/why-you-should-disable-graphql-introspection-in-production/i" style="pointer-events: none">usually disabled</a> in production systems.</div>
</div>

### System Design

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745256220551/d31a7e6d-c5ee-4957-b551-847dba74680a.png align="center")

For the MVP the system was quite simple (in my head)…

1. CronJob running somewhere (most likely either my old desktop or in the cloud) hits my accountability service every monday at 6am. All it should provide is the date range.
    
2. Accountability service hits my store of user data to get a list of users. This step will most likely involve retrieving configuration data associated with SplitWise and LeetCode, i.e id’s
    
3. Accountability service passes the list of users to the LeetCode client to find out the exact questions that the users completed in the time period. Collecting extra data here such as the type of question/difficulty could be useful for future enhancements in analytics.
    
4. The punisher⚒️ will then calculate the user penalty and how much each user owes each other.
    
5. REST Api call to SplitWise client to create an expense based on the penalty’s
    
6. A summary of the whole calculation is emailed for explainability (a critical feature that became more and more needed the more fines that were issued by the accountability service).
    

### Implementation

I’ll be honest, the implementation was definitely a scrappy process. For weekend projects I’ve started preferring a method of scrappy working code for a couple reasons:

1. It means I can actually finish in time (Priority Numero Uno)
    
2. Most of my weekend projects get left where they are so there’s no point over optimising/refactoring too early on
    

I roughly followed these steps:

**Step 1: API Exploration and Setup**

* As mentioned earlier I spent time exploring the various API’s I was going to be using
    
* After the initial exploration I made some quick and dirty python files for LeetCode & SplitWise clients (God bless [Cline](https://github.com/cline/cline) & ChatGPT as they definitely made this a lot quicker)
    

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text"><mark>Check for Rocks before you Dive!</mark> The first step of any project should be an initial experimentation of external dependencies (SDK’s, API’s etc). Without knowing what they support you can end up making a super detailed plan just to find out they don’t support any of what you were thinking.</div>
</div>

**Step 2: Building a CLI**

Decided to use Typer for my python CLI instead of my usual goto Click, as Typer simplifies the interface and builds upon Click's principles.

At this point i had a simple cli where you could pass in a list of leetcode usernames and it would return to you a summary of the questions they had solved

* ```bash
    python -m leetcode_accountability.cli bosire123 seth222 osagiog ioByte
    ```
    

**Step 3: Data Management**

The next step was deploying a super powerful database (CSV file) to store User Data/ any configuration. At its first iteration it just linked LeetCode usernames, SplitWise user IDs, and email addresses.

I intentionally avoided any normalization to keep the MVP straightforward.

```python
name,leetcode_id,splitwise_id,email_address,is_active
bos,bosire123,6996697,nyakundi@hotmail.co.uk,1
seth,seth222,103535402,set.wobble9d@icloud.com,1
mikkel,MikkelAllison,39202106,mikkel_a01@hotmail.co.uk,1
osa,osagiog,100418712,henchtechguy@gmail.com,1
emman,ioByte,73320769,e.okyere.eo@gmail.com,0
```

**Step 4: GitHub Actions for Automation**

For an automated trigger for the cli i ended up going with github actions. Partly because I used it before and partly because it also had an easy community action for sending an email after a job run :p

An example of what my workflow file looked like can be seen below:

```yaml
# .github/workflows/run-stats.yml
name: LeetCode Accountability Weekly Run

on:
  # Manual trigger with inputs
  workflow_dispatch:
  # Scheduled run every Monday at 6 AM London time (5 AM UTC)
  schedule:
    - cron: '0 5 * * 1'

jobs:
  run-stats:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.13'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run CLI with correct inputs
        run:
          python -m leetcode_accountability.cli weekly-run --output-type text
```

**Step 5: Expense Calculation & SplitWise Integration**

This step was basically implementing the Punishment Service

The first step was figuring out how the splitting of expenses will go (You can skip this part if you don’t care about how the splits are done😅)

* If one user a flops on `x` questions and the amount per question is `p` then `p*x` should be distributed between the other members of the group equally (`p` is £10 in most cases)
    
* So if there's `n` members including user a, then each member should get `(p * x)/(n-1)` or in the standard case `10x/(n-1)` and user a will be paying `10x`
    
* So if user a flops 3 questions and there's 6 people in the group, each person will get `10*3/(6-1)` = `30/5` = `£6`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745260015553/e1a65cd4-8490-4238-9e16-407a9a85871d.png align="center")

In step 1 I found out SplitWise has a [create expense endpoint](https://dev.splitwise.com/#tag/expenses/paths/~1create_expense/post) and has an option to split an expense equally or use the concept of shares, an example of a request object using shares is shown below

```json
{
  "cost": "25",
  "description": "Grocery run",
  ...
  "users__0__user_id": 54123,
  "users__0__paid_share": "25",
  "users__0__owed_share": "0",
  "users__1__user_id": 65432,
  "users__1__paid_share": "0",
  "users__1__owed_share": "25",
}
```

* The main parts here are the `users__0__paid_share` and the `users__0__owed_share`. From the definitions in the docs:
    
    * `users__0__paid_share`: Decimal amount as a string with 2 decimal places. The amount this user paid for the expense
        
    * `users__0__owed_share`: Decimal amount as a string with 2 decimal places. The amount this user owes for the expense
        
* Based on the object it means for a group of `n` users we have 2 methods
    
    * **Method 1:** Get the number questions missed per user, figure out what each user owes locally then upload one group transaction to SplitWise
        
    * **Method 2:** Get the number of questions missed per user, but instead of calculating how much each user owes locally, we could just create one transaction for each user
        
        * Method 2 makes things easier especially at the start as it offloads the logic of calculating who owes each other what to splitwise
            
        * Each transaciton can just be named after the person who flopped
            
        * ie if user a flops one week, splitwise will add a transaction called `user_a_failed_x_questions`
            
        * Helps alot with visibility as well
            

Decided to offload expense calculations directly to Splitwise using the "shares" feature.

* Looking back on our previous example, lets see what api calls we need per user flop.
    
* We are assuming user 0 has flopped twice, meaning if the threshold was seven questions then he owes `7-2 * £10` so £50. Lets assume as well that there is 3 users in total
    
* Lets also use the ids from my splitwise group
    

```json
{
  "cost": "50",
  "description": "User 0 Flop",
  "details": "User 0: 2 questions finished, 5 questions unfinished",
  "date": "2012-05-02T13:00:00Z",
  "repeat_interval": "never",
  "currency_code": "GBP",
  "category_id": 2,
  "group_id": 76659039,
  "users__0__user_id": 6996697,
  "users__0__paid_share": "0",
  "users__0__owed_share": "50",
  "users__1__user_id": 103535402,
  "users__1__paid_share": "25",
  "users__1__owed_share": "0",
  "users__2__user_id": 100418712,
  "users__2__paid_share": "25",
  "users__2__owed_share": "0"
}
```

Plugging this into splitwise got me this

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745260100484/72e99fc9-2e68-4f45-8ab2-1f31545ff447.png align="center")

This method simplified tracking penalties and provided clear visibility in Splitwise.

**Step 6: Putting it all together**

Integrating everything together was the final step, along with adding email notifications. For the emailing part, I simply updated the CLI to include an option for outputting as HTML. Then, I used a community GitHub Action and passed the HTML output to it.  
You can see an example of running the script for just one day (instead of seven) below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745260378099/92b7a159-0e69-4c82-b226-c549d6f802e9.png align="center")

And that was basically it :D my job of LeetCode enforcing has been passed and is in safer hands💪

### What I Learned

* **One-File Approach**: Initially coding everything in a single file greatly simplified debugging and rapid prototyping.
    
* **Early API Testing**: Conducting a mini-spike to verify API functionality before extensive planning saved considerable time.
    
* **Time Management**: Planning to complete within 10 hours was ambitious. Breaking it down into smaller chunks across days made completion manageable.
    
* **Authentication Pitfalls**: Minor oversights, like environment variables not loading correctly, can consume significant troubleshooting time.
    

### Future Improvements

Looking forward, several enhancements could expand the tool’s utility:

* Adding productivity graphs (like bar charts showing daily activity).
    
* Adding drop downs for each name in the email that shows the exact question a user completed with a timestamp so no one can argue with the bots decisions XD
    
* Extending notification methods (e.g., WhatsApp messages) for increased engagement.
    

This automation script has significantly streamlined our accountability processes, making it impossible to "forget" fines, thus enforcing discipline consistently. If you've been considering ways to enforce accountability effectively, automating penalty management might be just the solution you need😀.

### Images of Example Fines

* * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744787890717/3d6c6210-8b75-40f6-a3bc-9da0de975ccd.png align="left")
        
    * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744787916011/57d0e789-d2a2-4026-ae21-ffb9d783fbbb.png align="left")