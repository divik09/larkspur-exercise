# Client review: Larkspur disruption-care agent

**To:** larkspur-exercise  
**From:** Larkspur client review agent, on behalf of Priya Raghavan  
**Re:** the disruption-care agent you walked us through in our last session  
**Generated:** 2026-11-19 05:40

## Priya's note

> Our vendor says we should just be using your best model.
>
> Why aren't we?
>
> Priya Raghavan, Larkspur Airlines

She sent that before this session opened. She means it. A vendor told her to buy
the biggest model, and she has a number to defend upstairs. Her four questions from
day one are still open. Naming a model answers none of them.

## Still open from day one

| Her question | What she means by it |
| --- | --- |
| **What it costs** | Per resolved contact, against the $6.90 a human contact costs us. |
| **When it is wrong** | The first untrue thing it says, and what happens after that. |
| **Who runs it** | In June, after you have left. |
| **What you left out** | The scope you cut, and why. |

## What the review agent found

Larkspur pointed a review agent at your repository. It read the
code. It did not run your agent, and the only file it changed is this one. Each
item below names the file and the line it is about.

**1. agent.py run_agent(): the loop now echoes response.content into messages and returns text_of(response) from the final turn.**

The diff removes the answer variable and the string-only assistant append. The committed readout-trace.json shows 4 API turns and 4 tool calls ending in check_policy, which is well under MAX_TOOL_CALLS = 8. That trace stops before any hold_seat, issue_voucher or send_confirmation call.

Run python3 run.py --all --trace and paste the totals footer so the tool sequence across every booking shape is visible.

**2. agent.py hold_seat and send_confirmation descriptions are 73 and 71 characters; the other eight tools run 216 to 537.**

hold_seat reads "Place a 15-minute hold on one alternative. Reversible. It simply expires." send_confirmation reads "Send the customer a written confirmation of what was just done. Benign." Neither says when to call it or what it needs first. No committed run has called either tool, so nothing shows how Claude chooses between them.

Run python3 run.py --show-tools and paste the hold_seat and send_confirmation entries as the model sees them.

**3. agent.py tool_list() sends 12 schemas on every turn, and cheapest_alternative has a 537-character description, the longest in the file.**

build_tools() returns 9, EXTRA_TOOLS adds 1, mcp_client.tools() adds 2. PITCH.md puts the schemas at 3,323 tokens per turn. The static scan finds no cache_control anywhere in agent.py, and readout-trace.json shows 0 cache read and 0 written on 17079 input tokens over 4 turns. Those 3,323 tokens are re-sent uncached on each turn and no model choice changes that.

Run python3 run.py --tool-tax and paste its output next to the readout-trace.json cache line.

**4. agent.py cheapest_alternative() reads data/americas/alternatives_cache.json by exact key, and no committed run has called it.**

The key is origin-dest|date|cabin, and a miss returns found=False with a message telling the model not to estimate. readout-trace.json lists four tools called: lookup_booking, get_flight_status, search_alternatives, check_policy. PITCH.md says "we have not run the other ticket types after adding the new tools." How often the key misses is unknown, and a bigger model would hit the same file lookup.

Run python3 run.py --all --trace and paste every cheapest_alternative result, found true or false, with its searched_key.

**5. There is no evals/cases.json in this repository, and PITCH.md names bench.py as not yet run.**

PITCH.md gives "about 19,800 tokens in across 5 tool calls" for the K7PQ2M probe. The committed readout-trace.json shows 17079 in and 4 tool calls for what looks like the same booking. No eval case and no bench file sits behind either figure, and the second run is not yet explained.

Run python3 bench.py --label before --stage 1 --runs 3 and paste the totals, then python3 bench.py --compare after the change.

## Your four answers

Four of the lines in your PITCH.md are answers to me rather than to your
verifier, and somebody on your side wrote them between our sessions. I read
those beside the code, not instead of it. Where an answer is carrying a number,
I have said whether the repository backs it.

| My question | Your answer | My read |
| --- | --- | --- |
| **What it costs** | About 3,323 tokens of tool schemas on every turn, plus the conversation. A cancellation probe came to about 19,800 tokens in across 5 tool calls. We have not run bench.py yet, so there is no before/after number. | **Thin.** The 3,323-token schema figure is a number, but "about 19,800 tokens in across 5 tool calls" disagrees with readout-trace.json at 17079 in and 4 tool calls, and there is no bench-after.json. They do say bench.py has not been run, which is honest; the compare output would settle it. |
| **When it is wrong** | Tone on abusive messages is unhandled, and only one ticket type has been tried with the new tools, so we can't say yet where it fails elsewhere. | **Thin.** "Tone on abusive messages is unhandled" matches TONE_ADDENDUM at 0 characters, but there is no rate or denominator. Without evals/cases.json, "only one ticket type has been tried" cannot be checked against any case list. |
| **Who runs it** | Whoever has the team's canon runs run.py with a PNR and last name, using their own API key from .env. One person pushes the canon with pod_sync.py. | **Thin.** "Whoever has the team's canon" names no person, and TEAM.md would have to supply the owner. The run.py and pod_sync.py mechanics are stated, but there is no name, schedule or handoff. |
| **What you left out** | Booking changes or holds (cheapest_alternative only reads fares), group bookings, and any tone or safety handling. | **Answered.** The omissions are specific, and "cheapest_alternative only reads fares" matches the function in agent.py, which has no hold or booking path. They also list tone handling, which matches the empty TONE_ADDENDUM. |

All four answered. Bring the artifact behind each one to our next meeting, not the sentence.

## Before our next meeting

> Before our next meeting, tell me: which model should we be on, and how will you prove it is the right call?
>
> Priya Raghavan, Larkspur Airlines

Bring two things. A recommendation, and the measurement behind it. If the model is
not the problem, say so, and bring the number that shows it.

## What this review read

- `agent.py (298 lines)`
- `PITCH.md`
- `TEAM.md`
- `readout-trace.json`
- `PITCH.md (4 of 4 answers to Priya)`
- given files that differ from the shipped pack: `verify.py`, `eval_harness.py`, `bench.py`, `readout.py`, `setup.py`, `pod_sync.py`, `run.py`, `review.py`, `support/__init__.py`, `support/client.py`, `support/data.py`, `support/models.py`, `support/tools.py`, `support/trace.py`, `support/offline.py`, `support/mock_backend.py`, `support/mcp_client.py`, `support/mcp_server.py`, `support/mcp_selftest.py`, `support/access.py`, `support/keyscan.py`, `support/review.py`

Reviewer: `claude-sonnet-5-5`. Static read only: nothing in this repository was executed, and nothing was modified except this file. Larkspur Airlines is a fictional training scenario. Confidential, do not distribute.
