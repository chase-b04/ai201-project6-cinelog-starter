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

**What I did:** I made a new test file test_watchlist.py with the same test logic as test_add_to_collection_nonexistent_film_raises in test_collection.py but specific to test_watchlist. I had to change the name of the function and the add_to call. I also needed to add the correct imports, and make the same three pytest.fixture apps, which was copied from test_collection.py but to instead fix testing the watchlist instead.
**How I verified:** I verified by asking Claude if my test logic made sense and to point out anything that needed to be fixed. Afterwards, I ran "pytest tests/test_watchlist.py -v" on a film_id that doesnt exist to make sure that it passes, in which case it did within 0.44s.

## Comment 4 — Default visibility

**My position:** Keep public=True as the default.
**Reasoning:** The model already supports per-entry visibility, which only matters if there's a profile page or activity feed consuming it. That feature only works if entries are visible by default. Privacy toggles are low-engagement UI: if we shipped public=False, the majority of users would never find or flip the setting, and the social pages and feeds would silently stay empty, thus the feature would behave like a private list even though we built it to be shared. I'm optimizing for the common case of adding movies to watch which most people don't treat as sensitive, and for the bootstrapping a social watchlist feed feature actually doing what it was built for instead of dying from an opt-in.
**Tradeoff acknowledged:** The real cost is potentially violating user consent. A user adding a film to their watchlist is doing a tracking action, not making a privacy decision, and defaulting to public means some entries get shared before the user has consciously chosen that. An opt-in (public=False) default avoids this entirely and better matches "privacy by default" norms, at the cost of a colder emptier experience early on. Given that tradeoff, I think public=True is the right call if it's paired with a clear, discoverable way to opt out, such a toggle during oboarding, so the default is a starting point users can consciously reject rather than a silent decision made for them.

## Comment 5 — Sort order

**My position:** Agreed. I changed get_watchlist() in services/watchlist_service.py to order by WatchlistEntry.date_added.desc() instead of Film.title.asc().
**Reasoning:** A watchlist is a queue of intent, not a reference catalog. Users add a film because they want to watch it soon, and the entries at the top of that queue should be the ones they most recently decided mattered. Alphabetical order is a lookup pattern, useful when you already know what you're searching for, but it buries the thing a user just added under whatever else happens to start with an earlier letter.
**Engagement with reviewer's point:** don't have a strong counter-argument here, the reviewer's read on user intent matches how I'd actually use this feature myself. The one thing I'd add is that this decision should stay scoped to the watchlist. A user's completed collection is closer to a catalog people browse and revisit, so alphabetical order there still seems like the right default and shouldn't get swept into this change.

## Comment 6 — Rebase

**What conflicted:** Running: pytest tests/test_watchlist.py -v causes an ImportError while importing test module form the test_watchlist.py. There is an issue importing WatchListEntry from Models.
**How I resolved it:** I resolved this issue by going to Models.py, copying and pasting class CollectionEntry(db.Model), and rewriting it to be class WatchlistEntry(db.model), making sure the table arguments called from unique_user_film_watchlist as well.
**How I verified no conflict remains:** How I verified that there was no issues was by first fully going through the new function to make sure that there was nothing carried over from the collections function. Next, I reran pytest tests/test_watchlist.py -v, which passed now. I also ran pytest tests/ -v to make sure everything as whole passed as well.

## PR Description

<!-- Written at the end — feature overview, design decisions, manual testing steps -->
