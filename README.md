# counter
custom sequences

* simple sequence, inc\decr
* specific sequence, count by groups (track specific items)

Databases:
* clieckhouse
* es + mongodb (as storage)

TODO:
* добавить `datetime` для событий

## Clieckhouse

* docker
  * test?
* обзоро
  * счетчик
    * Писать все события и `SELECT sum(status) FROM data WHERE tid =? AND uid = ?;`
* очистка старых данных??
  * старыми данными являются те которые на каждое событие имеют две записи (со статусами `+1` и `-1`)
    * при условии что все новые сообщения в кротчайший срок становятся прочитанными то устаревшие данные можно удалять партиями (например за последний месяц)
    * относительно устаревшие событие не прочитанные переносить програмно в другую таблицу, оставшие данные (подтвержденные) удалять
      * `SELECT event_id, count() count FROM data WHERE user_id = ? GROUP_BY event_id HAVING count = 1;` события которые не имеют подтверждения

```
1 запись 16 байт (uuid) * 3 = 48 байт + ?? + integer?
Положим в группе по 3 человека
1000 групп
1000 сообщений

1000 * 3 * 1000 * 2 (unread+read) = 6 000 000 * ~50 байт = 300 * 10^6 байт = ~290 Мб
```

 
### Structure

``` sql
CREATE TABLE IF NOT EXISTS data (
 thread_id string,
 user_id string,
 event_id string,
 status integer -- '+1' если новое, '-1' если прочитано
);

-- INSERT INTO data VALUES(tid, uid, eid, 1); -- пользователь получил сообщение
-- INSERT INTO data VALUES(tid, uid, eid, -1); -- пользователь прочитал сообщение
-- SELECT sum(status) FROM data WHERE tid =? AND uid = ?; -- сколько сообщений у пользователя (в группе)
-- SELECT sum(status) FROM data WHERE uid = ?; -- сколько сообщений у пользователя всего
```
