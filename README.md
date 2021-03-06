acts-as-taggable-on

Komutlar,

	$ rails generate acts_as_taggable_on:migration
	$ rake db:migrate

User model,

	$ rails g model User name:string
	$ rake db:migrate

İlişkisellik, ![ilişki](http://yuml.me/fd245769.jpg)

	$ vim app/model/user.rb
	class User < ActiveRecord::Base
	  acts_as_taggable
	  acts_as_taggable_on :skills, :interests
	end

Konsol,

	> @user = User.new(:name => "Bobby")
	> @user.tag_list = ["awesome", "slick", "hefty"]
	> @user.skill_list = ["joking", "clowning", "boxing"]
	> @user.tag_list
	=> ["awesome", "slick", "hefty"]
	> @user.save

Tüm tag'leri bir arada görmek istersen,

	> y ActsAsTaggableOn::Tag.all
	ActsAsTaggableOn::Tag Load (0.3ms)  SELECT "tags".* FROM "tags"
	---
	- !ruby/object:ActsAsTaggableOn::Tag
	  attributes:
	    id: 1
	    name: awesome
	- !ruby/object:ActsAsTaggableOn::Tag
	  attributes:
	    id: 2
	    name: slick
	- !ruby/object:ActsAsTaggableOn::Tag
	  attributes:
	    id: 3
	    name: hefty
	- !ruby/object:ActsAsTaggableOn::Tag
	  attributes:
	    id: 4
	    name: joking
	- !ruby/object:ActsAsTaggableOn::Tag
	  attributes:
	    id: 5
	    name: clowning
	- !ruby/object:ActsAsTaggableOn::Tag
	  attributes:
	    id: 6
	    name: boxing
	=> nil

Kullanıcının tag'lerine bakalım,

	> y User.last.tags
	> y User.last.skills

Kullanıcıyı oluştururken doğrudan tag'leyebilirsin,

	> User.create(name: "Frankie", skill_list: "joking, flying, eating")

burada "foo, bar" aslında ["foo", "bar"] ile aynı.

Bunları saydırmak mümkün,

	> y User.skill_counts.all
	---
	- !ruby/object:ActsAsTaggableOn::Tag
	  attributes:
	    id: 4
	    name: joking
	    count: 2
	- !ruby/object:ActsAsTaggableOn::Tag
	  attributes:
	    id: 5
	    name: clowning
	    count: 1
	- !ruby/object:ActsAsTaggableOn::Tag
	  attributes:
	    id: 6
	    name: boxing
	    count: 1
	- !ruby/object:ActsAsTaggableOn::Tag
	  attributes:
	    id: 7
	    name: flying
	    count: 1
	- !ruby/object:ActsAsTaggableOn::Tag
	  attributes:
	    id: 8
	    name: eating
	    count: 1
	=> nil
	>

Ararken,

	> y User.tagged_with("awesome").all
	---
	- !ruby/object:User
	  attributes:
	    id: 1
	    name: Bobby
	    created_at: 2012-06-04 06:56:31.410198000 Z
	    updated_at: 2012-06-04 06:56:31.410198000 Z
	=> nil
	> y User.tagged_with("joking").all
	---
	- !ruby/object:User
	  attributes:
	    id: 1
	    name: Bobby
	    created_at: 2012-06-04 06:56:31.410198000 Z
	    updated_at: 2012-06-04 06:56:31.410198000 Z
	- !ruby/object:User
	  attributes:
	    id: 2
	    name: Frankie
	    created_at: 2012-06-04 07:03:04.630137000 Z
	    updated_at: 2012-06-04 07:03:04.630137000 Z
	=> nil
	>

Daha fazlası,

	> # hepsiyle eslessin
	> User.tagged_with(["awesome", "cool"], :match_all => true)

	> # herhangi birisiyle eslessin
	> User.tagged_with(["awesome", "cool"], :any => true)

	> # hicbirisiyle eslesmesin
	> User.tagged_with(["awesome", "cool"], :exclude => true)

Tag ve skill'i şu olanları bul demek için,

	> User.tagged_with(['awesome', 'cool'], :on => :tags, :any => true).
	       tagged_with(['smart', 'joking'], :on => :skills, :any => true)

## Relations

Girdi,

    @bobby = User.find_by_name("Bobby")
    @bobby.skill_list # => ["jogging", "diving"]

    @frankie = User.find_by_name("Frankie")
    @frankie.skill_list # => ["hacking"]

    @tom = User.find_by_name("Tom")
    @tom.skill_list # => ["hacking", "jogging", "diving"]

İlişki olanları bul,

    @tom.find_related_skills # => [<User name="Bobby">,<User name="Frankie">]
    @bobby.find_related_skills # => [<User name="Tom">]
    @frankie.find_related_skills # => [<User name="Tom">]

# Kaynak

- https://github.com/mbleigh/acts-as-taggable-on
