# sql2struct

Convert table creation statement to golang struct

Example:
```
CREATE TABLE `day_count` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `project_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT ' project_id ID',
  `created_at` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT 'created_at',
  `updated_at` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT 'updated_at',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uniq_project_id` (`project_id`),
  KEY `idx_created` (`created_at`),
  KEY `idx_updated` (`updated_at`)
) ENGINE=InnoDB AUTO_INCREMENT=795 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='total table';
```
To
```
type DayCount struct { 
Id int64 `json:"id"` // ID
ProjectId int64 `json:"project_id"` //  project_id ID
CreatedAt time.Time `json:"created_at"` // created_at
UpdatedAt time.Time `json:"updated_at"` // updated_at
}
```
