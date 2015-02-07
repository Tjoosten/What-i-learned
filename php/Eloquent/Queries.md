Eloquent Queries 
====================================

A short reference of Eloquent queries.

| Eloquent Query:                            | MySQL Query:                               |
| :----------------------------------------- | :----------------------------------------- |
| Album::find(1);                            | SELECT * FROM Album WHERE id = '1'         |
| Album::all()                               | SELECT * FROM Album                        |
| Album::where('column', 'operator', 'value' | SELECT * FROM Album WHERE column = 'value' |
