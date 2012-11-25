require 'rubygems'
require 'rake'


#
# Compile
#

desc "Compile everything"
task :compile do
  sh "coffee --compile --output js/compiled/ js/coffeescript/"
  sh "compass compile"
  sh "jekyll"
end


#
# Watch & continuously compile
#

desc "Watch-compile jekyll site"
task :jekyll do
  sh "jekyll --auto --server"
end

desc "Watch-compile coffeescript"
task :coffee do
  sh "coffee --compile --watch --output js/compiled/ js/coffeescript/"
end

desc "Watch-compile SCSS with Compass"
task :compass do
  sh "compass watch"
end


#
# Make creating posts easier
#

desc 'create new post or bit. args: type (post, bit), title, future (# of days)'
# rake new type=(bit|post) future=0 title="New post title goes here" slug="slug-override-title"
task :new do
  require 'rubygems'
  require 'chronic'
  
  type = ENV["type"] || "bit"
  title = ENV["title"] || "New Title"
  future = ENV["future"] || 0
  slug = ENV["slug"].gsub(' ','-').downcase || title.gsub(' ','-').downcase

  if type == "bit"
    TARGET_DIR = "_bits"
  elsif future.to_i < 3
    TARGET_DIR = "_posts"
  else
    TARGET_DIR = "_drafts"
  end

  if future.to_i.zero?
    filename = "#{Time.new.strftime('%Y-%m-%d')}-#{slug}.markdown"
  else
    stamp = Chronic.parse("in #{future} days").strftime('%Y-%m-%d')
    filename = "#{stamp}-#{slug}.markdown"
  end
  path = File.join(TARGET_DIR, filename)
  post = <<-HTML
--- 
layout: TYPE
title: "TITLE"
date: DATE
---

HTML
  post.gsub!('TITLE', title).gsub!('DATE', Time.new.to_s).gsub!('TYPE', type)
  File.open(path, 'w') do |file|
    file.puts post
  end
  puts "new #{type} generated in #{path}"
  system "open -a textmate #{path}"
end



#
# Deploy
#
task :default => :deploy


desc "Deploy to S3"
task :deploy do
  sh "jekyll"
  sh "s3cmd sync _site/* s3://www.shalinvs.tk"
  sh "rm -rf _site"
  sh "coffee --compile"
  sh "jekyll --lsi"
end