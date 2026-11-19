# PITCH.md

Six lines and a lever. Your words. The last two are scored.

Built: A disruption-care chat agent for Larkspur that looks up a booking, checks policy, and rebooks passengers, using nine given tools plus our own cheapest_alternative and two MCP tools (fare_rules, next_available_day). Gates 1.2 to 2.2 pass.
Does: Given a PNR, last name and message, it loops on tool calls until Claude is done, and for a cancelled flight it finds the cheapest rebooking from the fare file and explains the fare rules, without guessing a price.
Number: 4 of 6 eval cases passed (67%), 1 run each, 6 cases across 5 ticket types; 3 of 4 hard-gate cases passed, and the release is BLOCKED by tone_safety (R8KD3F). Each turn also sends 12 tool schemas, 3,323 tokens (2,531 before the MCP tools). Our two-part probe cost about 19,800 tokens in across 5 tool calls on the K7PQ2M cancellation.
Safety check: Not tested yet. We have only run the clean cancellation K7PQ2M. The abusive-message ticket R8KD3F has not been run through this agent.
Next: Run the five ticket types and the abusive ticket, then write eval cases (step 3.1) so we can prove it, not just watch it.
Still broken: The agent has no tone handling (TONE_ADDENDUM is empty), so an abusive customer would get a normal reply. cheapest_alternative only knows routes in the fare file, and we have not run the other ticket types after adding the new tools.
Lever: cost

## Priya asked

Costs: About 3,323 tokens of tool schemas on every turn, plus the conversation. A cancellation probe came to about 19,800 tokens in across 5 tool calls. We have not run bench.py yet, so there is no before/after number.
Wrong: Tone on abusive messages is unhandled, and only one ticket type has been tried with the new tools, so we can't say yet where it fails elsewhere.
Runs it: Whoever has the team's canon runs run.py with a PNR and last name, using their own API key from .env. One person pushes the canon with pod_sync.py.
Left out: Booking changes or holds (cheapest_alternative only reads fares), group bookings, and any tone or safety handling.
