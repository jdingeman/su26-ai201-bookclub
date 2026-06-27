request -> stats route -> stats service -> calculate_streak -> reading service -> get_reading_history -> ReadingEvent.query.filter_by...

alex.id = 5618d1df-2417-4947-b20d-33bcfe9693ee

http://127.0.0.1:5000/stats/5618d1df-2417-4947-b20d-33bcfe9693ee ==

```json
{
  "books_this_month": 3,
  "reading_streak": 0,
  "total_pages_read": 814,
  "user_id": "5618d1df-2417-4947-b20d-33bcfe9693ee"
}
```

http://127.0.0.1:5000/reading/history/5618d1df-2417-4947-b20d-33bcfe9693ee ==

```json
[
  {
    "book": {
      "added_at": "2026-04-08T04:42:49.078844",
      "added_by": "5618d1df-2417-4947-b20d-33bcfe9693ee",
      "author": "James Baldwin",
      "genre": "literary fiction",
      "id": "16fa209c-e961-40ef-adc7-b38fdbe079c2",
      "pages": 176,
      "title": "Giovanni's Room"
    },
    "book_id": "16fa209c-e961-40ef-adc7-b38fdbe079c2",
    "finished_at": "2026-06-25T04:42:49.078844",
    "id": "ed773b44-7578-4d05-9b6b-0a6904625f01",
    "started_at": "2026-04-18T04:42:49.078844",
    "user_id": "5618d1df-2417-4947-b20d-33bcfe9693ee"
  },
  {
    "book": {
      "added_at": "2026-04-03T04:42:49.078844",
      "added_by": "5618d1df-2417-4947-b20d-33bcfe9693ee",
      "author": "Octavia E. Butler",
      "genre": "sci-fi",
      "id": "1e6af02b-33bd-4aee-937c-9340600f2751",
      "pages": 352,
      "title": "Parable of the Sower"
    },
    "book_id": "1e6af02b-33bd-4aee-937c-9340600f2751",
    "finished_at": "2026-06-26T04:42:49.078844",
    "id": "6ffa7dd0-5af5-413d-afb9-bb0b71462183",
    "started_at": "2026-04-10T04:42:49.078844",
    "user_id": "5618d1df-2417-4947-b20d-33bcfe9693ee"
  },
  {
    "book": {
      "added_at": "2026-03-29T04:42:49.078844",
      "added_by": "5618d1df-2417-4947-b20d-33bcfe9693ee",
      "author": "Ursula K. Le Guin",
      "genre": "sci-fi",
      "id": "8c2e0eff-6ff7-475c-8aa9-e27af40cf8e8",
      "pages": 286,
      "title": "The Left Hand of Darkness"
    },
    "book_id": "8c2e0eff-6ff7-475c-8aa9-e27af40cf8e8",
    "finished_at": "2026-06-27T01:42:49.078844",
    "id": "f92d8604-5efa-4c02-9742-419586f50ba8",
    "started_at": "2026-04-03T04:42:49.078844",
    "user_id": "5618d1df-2417-4947-b20d-33bcfe9693ee"
  }
]
```

Should be 3 day streak based on finished_at.
Should be returning books finished in reverse order than what is returned currently.

The docstring says: A streak is the number of consecutive calendar days on which the user
finished at least one book, counting back from today (or yesterday, if
nothing has been finished today yet).
The code does: dates = sorted(
set(e.started_at.date() for e in events),
reverse=True,
)
The bug is on line: 35
The fix is: Change started_at to finished_at

## Post fix

alex.id = 9142172b-25be-40d3-bd27-0881dc128004

Streak calculation now correct. But books still return in wrong order

first book listed ==

```json
        "book": {
            "added_at": "2026-04-08T05:29:22.952010",
            "added_by": "9142172b-25be-40d3-bd27-0881dc128004",
            "author": "James Baldwin",
            "genre": "literary fiction",
            "id": "3bcf551d-9043-4523-8ec2-f03b32ed53d1",
            "pages": 176,
            "title": "Giovanni's Room"
        },
        "book_id": "3bcf551d-9043-4523-8ec2-f03b32ed53d1",
        "finished_at": "2026-06-25T05:29:22.952010",
        "id": "35bf3839-c1ef-4ae1-b3f9-cf1867a4e1c7",
        "started_at": "2026-04-18T05:29:22.952010",
        "user_id": "9142172b-25be-40d3-bd27-0881dc128004"
```

The docstring says: Return the books a user has finished, most recently finished first.
The code does: return (
ReadingEvent.query.filter_by(user_id=user_id)
.filter(ReadingEvent.finished_at.isnot(None))
.order_by(ReadingEvent.started_at.desc())
.all()
)
The bug is on line: 124
The fix is: replace started_at with finished_at

## Post fix bug 2

alex.id = d3429949-d3fd-4e61-b7d9-037f257f7bce
