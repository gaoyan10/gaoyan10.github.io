---
layout: post
title: 在rails中使用postgresql的jsonb字段
categories: [Ruby]
tags: [ruby, Rails, postgresql]
---

###需求
最近项目中碰到个需求。需求是这样的。

角色需要有

1. 基础信息（账号，密码，邮箱等）
2. 工作经验 (公司，年份，职位)  
3. 教育背景（学校，年份）  
4. 个人链接（名称，链接）  
5. 其他

### 原始设计
在设计模型过程中，考虑到多种角色，所以基础信息部分用**User**模型管理。在设计针对的模型**Angel**管理其他信息。
按照传统的关系型设计。2，3，4，5都是数组。需要拆分出另外的model映射管理。伪代码如下

```ruby
class User < ActiveRecord::Base
    #account
    #email
    #password
end
class Angel < ActiveRecord::Base
    belong_to :user
    has_many :work_experiences
    has_many :links
    has_many :educations
    #has_many ...
end

class WorkExperience < ActiveRecord::Base
    #company
    #time
    #title
end

...
```

###jsonb解决方案
这样设计也可以，不过考虑到这个功能并不常用。而且字段其实相对不固定。采用nosql的其实效果更好。
于是考虑采用postgresql的jsonb类型解决。另外采用nosql还需要考虑1个问题是，
为了防止有人恶意发包串改数据，所以数据部分要进行字段和类型的校验。

经过搜索找到如下资料  
[Postgresql JSON Functions and Operators][postgresqlurl]

[Active Record and PostgrelSQL][ActiveRecordpostgresql]

[A simple Json column with validation for rails and PostgreSQL Json/Jsonb field][jsoncolumn]

###实现

1. 新建数据库迁移，生成字段
2. 配置模型中scope，用来根据jsonb中的字段进行查找
3. 引入json_column gem包
4. 配置对应json的校验信息

最后给出部分代码

1. 迁移文件

```ruby
class AddAngelJsonDataToAngel < ActiveRecord::Migration
  def change
    add_column :angels, :info, :jsonb, default: '{"cases":[],"work_experiences":[],"educations":[],"links":[]}'
    add_index :angels, :info, using: :gin
  end
end
```
2. 模型文件

```ruby
class Angel < ActiveRecord::Base
  belongs_to :user

  has_and_belongs_to_many :startups

  acts_as_json_column columns: [info: :AngelInfoSchema]
  validates :info, json: { schema: Schemas::AngelInfoSchema.schema.to_json }


  default_scope           { order(id: :desc) }
  scope :cases,           ->(query){ where('"info"->\'cases\' @> ?', [query].to_json)             }
  scope :work_experiences,->(query){ where('"info"->\'work_experiences\'  @> ?', [query].to_json) }
  scope :educations,      ->(query){ where('"info"->\'educations\' @> ?', [query].to_json)        }
  scope :links,           ->(query){ where('"info"->\'links\' @> ?', [query].to_json)             }

end
```

3. schema文件

```json
{
  "type" : "object",
  "properties" : {
    "cases" : {
      "type": "array",
      "items" : {
        "type" : "object",
        "properties" : {
          "title" : "string",
          "time" : "string",
          "content" : "string"
        }
      }
    },
    "work_experiences"  : {
      "type": "array",
      "items" : {
        "type" : "object",
        "properties" : {
          "title" : "string",
          "time" : "string",
          "content" : "string"
        }
      }
    },
    "educations" : {
      "type" : "array",
      "items" : {
        "type" : "object",
        "properties" : {
          "title" : "string",
          "time" : "string"
        }
      }
    },
    "links" : {
      "type": "array",
      "items" : {
        "type" : "object",
        "properties" : {
          "title" : "string",
          "link" : "string"
        }
      }
    }
  }
}
```

###实际使用


```ruby
#插入
angel = Angel.new
angel.info['links'] << {"title":"个人博客", "link":"http://blog.watsy0007.com"}
angel.save

#查询
angels = Angel.links("link":"http://blog.watsy0007.com")
```

that's all

[postgresqlurl]:http://www.postgresql.org/docs/current/static/functions-json.html
[activeRecordpostgresql]:http://edgeguides.rubyonrails.org/active_record_postgresql.html
[jsoncolumn]:https://github.com/pywebdesign/json_column
