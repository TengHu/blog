# Memory, identity, and multi-user agents

Some lessons from deploying OpenClaw for my team recently.

## What goes wrong

The problem comes from combining persistent memory with multiple users. Once you are in that regime, you are not dealing with one question; you are dealing with several, and they are easy to mix up.

1. Identity: who is talking to me? That one is mostly solved in principle, and a lot of vendors are racing to be the layer.

2. Data access: what data am I allowed to retrieve for this person? That is still being worked on, and the point is that the distinction has to be enforced around the model, not waved away inside it.


Then there is a third problem, which is not the same as the second: there is stuff the agent should know but should not say. That is output discretion — given everything I know, what should I say versus not say to this specific person? — and it is barely addressed AFAIK.

## Some Examples

- The agent knows the company is about to do a layoff/re-org from exec conversations, and a junior employee asks how the company is doing. The agent should not lie, but it also should not reveal the exec decisions. What does it say?

- You told the agent you are struggling with a project, and your teammate asks the agent how the project is going. The agent has your candid assessment. Should it share that, soften it, or stay silent?

- You are in a team Slack with a shared AI agent. You have had private DMs with the agent where you said you are thinking of changing teams or that you think the project is going to fail. Now your manager asks the agent in a group channel what the team’s morale is like. The agent already has your context in its memory or session history.

Taken together, this is essentially asking the AI to have social/workplace intelligence: to understand that information has audiences, that context determines appropriateness, and that what you say depends on who you are saying it to. That is not an auth problem or a database problem. It is something much harder.

## "One brain, many mouths"

I call this problem “one brain, many mouths”: one persistent system answering different people in different contexts. Under that shape, data access and output discretion pull in different directions, and both matter.

### Fuzzy data access

Roughly, you want something like IAM for the agent, plus input and output filtering so that if the agent does not see something, it will not say it. Where this actually breaks is that there are fuzzy visibility boundaries. For example, if the agent has access to a private HR channel and a public engineering channel and it carries information from one into the other. The agent has no concept of “private”; it just sees text coming in.

Think about all the surfaces where you interact with an agent in a workspace:

- a DM with the agent
- Zoom transcripts
- a private Slack channel with three people and the agent
- a public Slack channel with the agent
- an email thread the agent is cc’d on and a calendar the agent can check.


To a human, each of these carries a completely different social expectation of privacy. You say things in a DM you would never say in a public channel, and you write things in a private doc you would never put in Jira. To the agent, it is all just text input. There is only limited metadata—the name of the channel, how many people are in the channel, and so on—and the agent does not distinguish between a DM and a town hall announcement. It processes both with the same access and the same willingness to reference later. There is no standard permission model (yet) for the agent to treat this conversation differently from that conversation.

So when you define data access for an agent, it is not just the traditional IT problem of which databases and files this user can see. It is a much wider surface. Traditionally, access control was built for storage systems: files, databases, repos. Conversations are not objects; they are contexts, and the privacy expectation comes from the context. Nobody tags a Slack DM with sensitivity: confidential; nobody labels a one-on-one meeting transcript with audience: manager-only. The privacy is implied by the channel type, the participant list, the social norm — and the agent is completely blind to all of it. What we would probably need is a new dimension of implied access control: source context (where did this information come from?) × audience context (who is the agent talking to right now?) × implied sensitivity (what is the social expectation of the source?).

### Output discretion

This is hard, and it is where workplace or social intelligence has to come in, through training, fine-tuning, or prompting. The issue is that agents do not self-limit: they pull from everything they can access to give a helpful answer. And data access problems and output discretion problems compound each other too.

## Isolation and explicit sharing

One answer is not hard to picture: isolated agents that selectively publish to a shared knowledge layer. Each person’s agent is private by default. When you solve a problem, you choose to publish the solution to a team knowledge base. Your private conversations stay private, and only explicitly shared knowledge crosses the boundary. That is basically how humans already work: you share outputs — solutions, decisions, documents — while the messy drafts, the uncertainty, and the private complaints stay in your head.

The problem is that this product story could feel less magical. It takes human effort to decide what to share. And I argue the most valuable knowledge could come from conversations, not documents — preferences, thinking out loud, key observations, intuition, debate, half-formed ideas. One brain, many mouths is valuable, but the value is mostly to the organization, not to the individual. The individual bears the privacy risk and the organization captures the knowledge benefit. That asymmetry drives companies to race toward shared brains.

Most memory systems today still throw chat and docs into the same retrieval pile with no provenance, so “private by default, publish only what you mean to” is hard to enforce in practice.
