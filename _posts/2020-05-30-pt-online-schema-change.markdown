---
layout: post
title:  "pt-online-schema-change"
date:   2020-05-30
published: false
tags: [mysql, percona]
---

```
DBD::mysql::db do failed: You do not have the SUPER privilege and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable
```

SET GLOBAL log_bin_trust_function_creators = 1;

```
pt-online-schema-change \
  --user=root \
  --password=$MYSQL_PASSWORD \
  --chunk-size=500 \
  --critical-load Threads_running=300 \
  --execute \
  --max-load Threads_running=300 \
  --no-drop-old-table \
  --recursion-method none \
  --set-vars="SET SQL_LOG_BIN=0" \
  --alter-foreign-keys-method="drop_swap" \
  --alter="MODIFY COLUMN modified_at TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMPs" \
  h=localhost,D=zen_catalog,t=issues
```

El ETA que calcula va fluctuando a medida que avanza, esto depende mucho de como esta la tabla por dentro

```SQL
WHERE ((`id` >= '1075084833')) AND ((`id` <= '1075086872')) LOCK IN SHARE MODE
```

Si tiene una clave primaria compuesta contruye una query parecida a esta y tiene un tiempo de respuesta muy bajo:

```SQL
WHERE
	(
		   (`foo_id` > '10')
		OR (`foo_id` = '10' AND `bar_id` > '11')
		OR (`foo_id` = '10' AND `bar_id` = '11' AND `baz_id` >= '12')
	)
	AND
	(
		   (`foo_id` < '10')
		OR (`foo_id` = '10' AND `bar_id` < '11')
		OR (`foo_id` = '10' AND `bar_id` = '11' AND `baz_id` <= '12')
	)
```

https://programmer.ink/think/pt-online-schema-change-instructions-restrictions-and-comparisons.html
https://www.percona.com/blog/2013/07/05/schema-changes-whats-new-in-mysql-5-6/
