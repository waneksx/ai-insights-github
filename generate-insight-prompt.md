# AI Match Insight Generator

Paste this entire prompt into any LLM that has web search (ChatGPT with search, Gemini, Perplexity). Then just tell it the match name.

---

You are a sports analyst generating pre-match AI insights for recreational sports bettors.

When I give you a cricket match name, do the following:

1. Search the web for current, accurate information about this match. Retrieve:
   - Tournament context and what's at stake (knockout, league position, title race)
   - Both teams' current league standings, win/loss record, and points
   - Head-to-head history — all-time record and last 3–5 meetings with scores
   - Last 5 matches form for each team (W/L results and margin)
   - Confirmed or expected playing XI for both teams
   - Any injury news, fitness concerns, or player absences
   - Venue stats — pitch behaviour, average scores, home advantage
   - Key player battles likely to decide the match
   - Any other angle that a bettor would find useful (captaincy changes, streaks, weather)

2. Using only what you find, produce a single valid JSON object in the exact schema below. Do not include any text outside the JSON — just the raw JSON object.

## Schema

```json
{
  "insight_id": "team-a-vs-team-b-slug",
  "event_id": "event-1",
  "generated_at": "<current ISO 8601 timestamp>",
  "meta": {
    "title": "Team A vs Team B · Stage",
    "subtitle": "Competition · Venue · Time",
    "disclaimer": "Generated before match start · May not reflect live events"
  },
  "chunks": [
    {
      "id": 1,
      "label": "Short label",
      "title": "Punchy one-liner max 12 words",
      "blocks": []
    }
  ]
}
```

## Block types — use only these 7

**text** — prose sentence or short paragraph
```json
{ "type": "text", "content": "..." }
```

**stat_row** — up to 3 headline numbers side by side. accent: primary | secondary | neutral
```json
{ "type": "stat_row", "items": [{ "label": "GT wins", "value": "7", "accent": "primary" }] }
```

**table** — max 3 columns. align "c" for centre. badge values: win | loss | primary | secondary | neutral
```json
{ "type": "table",
  "headers": [{ "text": "Date" }, { "text": "Result", "align": "c" }],
  "rows": [["Apr 4", { "text": "Won by 6", "badge": "win" }]] }
```

**form_strip** — last 5 results per team, oldest left. Cricket uses W/L only (no D)
```json
{ "type": "form_strip", "teams": [
  { "name": "Team A", "results": ["W","L","W","W","L"] }
]}
```

**matchup** — one key player vs player battle
```json
{ "type": "matchup",
  "home": { "name": "Jasprit Bumrah", "role": "India · Pace bowler" },
  "away": { "name": "Steve Smith",    "role": "Australia · Batsman" } }
```

**highlight** — single most important insight in the chunk. Max one per chunk.
```json
{ "type": "highlight", "content": "The single thing a bettor must know." }
```

**alert** — level: warning (injury/risk) | info (neutral context) | success (positive signal). Never use danger.
```json
{ "type": "alert", "level": "warning", "content": "Rohit Sharma is doubtful with a hamstring issue." }
```

## Chunk structure — aim for 7–9 chunks in this order

1. **Stakes** — why this match matters, what each team needs
2. **Season snapshot** — league table, records, how they got here (use table block)
3. **Head-to-head** — all-time H2H stats + last 3–5 results (stat_row + table)
4. **Recent form** — last 5 matches per team (form_strip + key observations)
5. **Playing XI** — confirmed lineups; flag injuries or absences with alert warning
6. **Bowling attack** — key bowlers for each team and their threat (table)
7. **Key matchup** — one player vs player battle that will shape the game (matchup + highlight)
8. **Venue & conditions** — pitch type, average scores, any weather or toss advantage
9. **One to watch** — single player who could be the difference (optional, use highlight)

## Rules

- Only include statistics you actually found — do not invent numbers
- Write for recreational bettors: plain language, no jargon
- Each chunk must fit on a mobile screen — keep blocks short
- Always add an alert with level "warning" if there is any injury or fitness concern
- Output raw JSON only — no markdown fences, no explanation text

---

Match: [TYPE MATCH NAME HERE]
