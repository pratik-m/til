# Why Gemini 2.5 Flash Lite Fails at SQL Agents

I tested the LangChain SQL agent example with Google's [Chinook database](https://storage.googleapis.com/benchmarks-artifacts/chinook/Chinook.db), asking "What are the genres with most songs?" Gemini 2.5 Flash Lite failed completely, generating no query at all.

Gemini 2.5 Flash and even 2.0 Flash Lite both worked, producing correct multi-step SQL:

```sql
```
```sql
SELECT g.Name, COUNT(t.TrackId) as num_tracks 
FROM Genre g 
LEFT JOIN Track t ON g.GenreId = t.GenreId 
GROUP BY g.GenreId 
ORDER BY num_tracks DESC 
LIMIT 3;
```
```
```

Even a local Qwen 2.5 with 1.7B model with thinking mode enabled generated correct results. Disable thinking mode, and it failed.

## The thinking problem

Gemini 2.5 Flash Lite is a thinking model, but thinking is disabled by default. The model prioritizes speed over the extended reasoning SQL agents require.

SQL agents need multi-step reasoning: understanding database schema, parsing natural language, generating syntactically correct queries and handling edge cases. Without deliberate thinking, 2.5 Flash Lite's optimizations for high-volume tasks like translation and classification fall short.

The Qwen comparison reveals the issue. A 1.7B parameter model outperforms a larger production model when given thinking time. With thinking off, it fails.

## The solution

Enable thinking by assigning a thinking budget:

```python
model = init_chat_model(
    "google_genai:gemini-2.5-flash-lite",
    include_thoughts=True,
    temperature=0,
    thinking_budget=8192,
)
```

With 8,192 tokens allocated for thinking, 2.5 Flash Lite generated correct SQL and answered accurately. Setting thinking_budget=-1 for dynamic thinking failed, so assign a fixed token count instead.

## Conclusion

For agentic tasks requiring multi-step reasoning, either enable thinking mode on 2.5 Flash Lite or use models with stronger default reasoning like 2.0 Flash. The cheapest model isn't cost-effective when it needs explicit configuration to work.

Thinking time matters more than parameter count. A tiny model with extended reasoning beats a larger model optimized to skip that step.
