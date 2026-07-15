# PR Response Doc — CineLog Watchlist Feature

## AI Usage

<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename

**What I did:** What I did was I renamed save_to_watchlist() to add_to_watchlist() in services/watchlist_service.py, as well as changing two mentions of the original save_to_watchlist() function in routes/watchlist/watchlist.py
**How I verified:** I verified by using my code editors search to find all the references of "save_to_watchlist" to make sure I didn't miss any mentions of it.

## Comment 2 — Deduplication

**What I did:** I edited add_to_watchlist() in services/watchlist_service.py by adding an "existing" boolean value that was a WatchlistEntry query that is filtered by userid and filmid. I then used an if statement to check if "existing" is true, and if it was it would call a AlreadyInWatchlistError, in which I supplementarily created as a class on top of add_to_watchlist().
**How I verified:** How I verified is I went to services/collection_service.py and checked out it's add_to_collection() and say the differences inbetween it and add_to_watchlist()'s program, and noticed a block of code that mentioned above, everything else looked identical logically. I reran through the deduplication logic of both functions and now both line up.

## Comment 3 — Missing test

**What I did:**
**How I verified:**

## Comment 4 — Default visibility

**My position:**
**Reasoning:**
**Tradeoff acknowledged:**

## Comment 5 — Sort order

**My position:**
**Reasoning:**
**Engagement with reviewer's point:**

## Comment 6 — Rebase

**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description

<!-- Written at the end — feature overview, design decisions, manual testing steps -->
