---
layout: navpage
sidebar: mappers
title: "DbSettingMapper"
description: "A mapper class for all the database setting classes. The mapping can be made based on the type of the target RDBMS data provider."
permalink: /mapper/dbsettingmapper
tags: [repodb, class, dbsettingmapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# DbSettingMapper

A mapper class for the [IDbSetting](/interface/idbsetting)-based class. The mapping can be made based on the type of the target RDBMS data provider. Please visit the [Database Setting](/extensibility/databasesetting) for more information.

#### Methods

Below are the methods available from this class.

- `Add` - adds a mapping between the [IDbSetting](/interface/idbsetting) and the type of the `DbConnection`.
- `Clear` - clears all the mappings for the database settings.
- `Get` - gets the mapped [IDbSetting](/interface/idbsetting) based on the type of the `DbConnection`.
- `Remove` - removed the mapping between the [IDbSetting](/interface/idbsetting) and the type of the `DbConnection`.

#### Use-Cases

You should use this class if you would like to override the default mapping of the library when it comes to database setting.

#### How to use?

To add a mapping, simply call the `Add` method.

```csharp
DbSettingMapper.Add<SqlConnection>(new MyCustomSqlServerDbSetting(), true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get` method.

```csharp
var dbSetting = DbSettingMapper.Get<SqlConnection>();
```

To remove the mapping, use the `Remove` method.

```csharp
DbSettingMapper.Remove<SqlConnection>();
```

