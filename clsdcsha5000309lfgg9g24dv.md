---
title: "UNIQUE constraints in SQLite FTS (FTS3/FTS4) tables"
seoTitle: "UNIQUE constraints in SQLite FTS (FTS3/FTS4) tables"
datePublished: Mon May 12 2014 23:37:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdcsha5000309lfgg9g24dv
slug: unique-constraints-in-sqlite-fts-fts3fts4-tables
canonical: https://roderickkennedy.com/dbgdiary/unique-constraints-in-sqlite-fts-fts3fts4-tables

---

The [correct](http://stackoverflow.com/questions/9880644/using-sqlite-create-virtual-table-using-fts3-with-unique-column-dosnot-stay-uniq) way to do this:

CREATE VIEW playlist\_view AS SELECT \* FROM playlist;

CREATE TRIGGER insert\_playlist INSTEAD OF INSERT ON playlist\_view

BEGIN

SELECT RAISE(ABORT, 'column user\_id is not unique') FROM playlist

WHERE user\_id=new.user\_id;

INSERT INTO playlist (from\_user, from\_id, created\_time,

created\_time\_formated, user\_id)

VALUES (NEW.from\_user, NEW.from\_id, NEW.created\_time,

NEW.created\_time\_formated, NEW.user\_id);

END;

\-- And you do the insertion on the view

INSERT INTO playlist\_view (from\_user,from\_id,created\_time,created\_time\_formated,user\_id) SELECT ...;