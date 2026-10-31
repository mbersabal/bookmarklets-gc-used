# GC Used: "Available Since" Bookmarklet

See when a Guitar Center **Used** listing became available, right from the listing page. No install, no extension, and it works on iPad Chrome.

> **Unofficial tool.** Not affiliated with Guitar Center. It only reads the page you already have open in your browser. It does not scrape, load other pages, or send anything anywhere.

## Why this exists

Used gear that sits at a store for a long time is often easier to negotiate on. Guitar Center doesn't show how long a listing has been up, but the page's hidden data includes an `availableDate` field. This bookmarklet finds it and turns it into a readable date and a day count.

## What you'll see

A popup like this:

```
Available since: Oct 14, 2026
17 days ago

Resets on price drops, returns or relists. Under 90 days = true age unknown.

Date picked: nearest to item ID 122926427 (distance 70)
```

If the item has been available for 90+ days, the popup adds **(90+ DAYS, no change since)**.

## Set it up (iPad or iPhone Chrome)

1. Open any web page in Chrome and bookmark it: tap the **share icon**, then **Add Bookmark**.
2. Tap **...** then **Bookmarks**, find the new bookmark, and edit it. (If your version won't let you edit it, delete it and create a new bookmark the same way.)
3. Name it `availablesince`.
4. Replace the URL with the code below, all on one line.
5. Tap **Done**.

```
javascript:(function(){var h=document.documentElement.outerHTML;var id=(location.pathname.match(/(\d{6,})\.gc/)||[])[1]||((document.body.innerText.match(/Item #:?\s*(\d{6,})/)||[])[1]);var ids=[],i=-1;if(id){while((i=h.indexOf(id,i+1))>-1)ids.push(i);}var re=/"availableDate"\s*:\s*(\d{10,13})/g,m,r=[];while((m=re.exec(h))){var t=+m[1];if(t<1e12)t*=1000;var dist=ids.length?Math.min.apply(null,ids.map(function(p){return Math.abs(p-m.index)})):999999;r.push({t:t,dist:dist});}if(!r.length){alert('No availableDate found');return;}r.sort(function(a,b){return a.dist-b.dist});var x=r[0];var days=Math.floor((Date.now()-x.t)/864e5);alert('Available since: '+new Date(x.t).toLocaleDateString('en-US',{month:'short',day:'numeric',year:'numeric'})+'\n'+days+' days ago'+(days>=90?' (90+ DAYS, no change since)':'')+'\n\nResets on price drops, returns or relists. Under 90 days = true age unknown.\n\nDate picked: nearest to item ID '+id+' (distance '+x.dist+')');})();
```

## How to use it

1. Open a Guitar Center Used **listing page** (the page for one item, not search results).
2. Tap the address bar and **type** `availablesince`.
3. Tap the bookmark when it shows up in the suggestions. On iPad Chrome, tapping the bookmark from the Bookmarks menu does not run it. It has to be launched from the address bar.
4. Read the popup.

On desktop Chrome, you can also click the bookmark from your bookmarks bar.

## How to read the result

| Result | What it means |
|---|---|
| **90+ days** | Strong signal. Nothing has reset the date in 3+ months, so the item has sat there. Good candidate for an offer. |
| **Under 90 days** | Ambiguous. It could be a new listing, or an older one that was reset. Don't treat it as the true age. |
| **No availableDate found** | The page layout may have changed, or you're not on a listing page. |

## Known limits (from hands-on testing)

- **The date appears to reset.** In testing, a months-old guitar with a price drop showed about 22 days, and an amp that had been listed for well over a day showed 1 day (possibly returned and relisted).
- **What triggers a reset is not confirmed.** Price drops, returns and relists are the likely causes, but that's observed behavior, not documented by Guitar Center.
- **Small sample.** This was tested on only a handful of listings. Please check it against items you know well and report what you find.
- **The site can change.** If Guitar Center renames or moves the field, the bookmarklet will stop working.
- **A tip for negotiating:** a price drop that resets the date is itself a sign the store is already moving the item.

## Finding the longest-listed items (the "oldest first" workaround)

Guitar Center has no "oldest" sort and no URL setting for it. But the **Newest** sort (`Ns=cD`, probably "created date") appears to use a different date than the bookmarklet, one that does **not** reset on price drops. So the *last pages* of the Newest sort behave like an oldest-first list.

1. Open a category or brand page and sort by **Newest**. The address will include `Ns=cD`.
2. Narrow it with filters (brand, price, category) until there are only a few hundred results. On huge categories, the search may stop paging before it reaches the oldest items.
3. Jump to the **last pages**. These are the longest-listed items.
4. Open candidates and run the bookmarklet:
   - **Old date + deep in the list:** hasn't changed in months. Best offer candidate.
   - **Recent date + deep in the list:** probably had a price drop or return. The store is already moving it.

**What this is based on (small test, one brand):** in a Suhr search sorted by Newest, a 3-day item was first, a 213-day item was second to last, and a 70-day item with a price drop was last. A months-old guitar with a price drop (22 days) landed on page 3 of 4. If the sort used the same date as the bookmarklet, those would not line up. This is observed behavior, not documented, so please test it on your own brands.

## How it picks the date

A listing page can include dates for the item you're viewing and for "similar items." The bookmarklet finds every `availableDate` on the page and picks the one closest to the listing's own item number in the page data. The "distance" in the popup shows how close that was. A small number means a good match.

## Roadmap / ideas

- [ ] Track first-seen dates and price changes for items you watch (more accurate than a field that resets)
- [ ] Browser extension that shows the date as a badge right on the listing
- [ ] Highlight listings from stores you personally had bad experiences with (always highlight, never hide)
- [ ] More testing on a variety of listings

## Disclaimer

Use at your own risk. This is a personal convenience tool that reads only what your browser has already loaded. Be sure your use fits Guitar Center's website terms. No guarantee of accuracy.

## Changelog

- **v1:** Shows the `availableDate` closest to the listing's item ID, with day count and a 90+ day flag.
- **v1.1:** Some listing URLs have no item number at the end. The bookmarklet now falls back to the "Item #" shown on the page. Added the Newest-sort workaround.
