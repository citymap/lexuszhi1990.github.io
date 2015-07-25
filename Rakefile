require "rubygems"
require "tmpdir"
require "bundler/setup"
require "jekyll"
require 'chinese_pinyin'

# Change your GitHub reponame
GITHUB_REPONAME = "lexuszhi1990/lexuszhi1990.github.io"

namespace :site do
  desc "Generate blog files"
  task :generate do
    Jekyll::Site.new(Jekyll.configuration({
      "source"      => ".",
      "destination" => "_site"
    })).process
  end


  desc "Generate and publish blog to gh-pages"
  task :publish => [:generate] do
    Dir.mktmpdir do |tmp|
      cp_r "_site/.", tmp

      pwd = Dir.pwd
      Dir.chdir tmp

      system "git init"
      system "git add ."
      message = "Site updated at #{Time.now.utc}"
      system "git commit -m #{message.inspect}"
      system "git remote add origin git@github.com:#{GITHUB_REPONAME}.git"
      system "git push origin master:refs/heads/gh-pages --force"

      Dir.chdir pwd
    end
  end
end

desc 'default task'
task :default do
    sh 'jekyll server'
    sh 'open http://127.0.0.1:4000'
end

desc 'Make a new post'
task :post do
  print 'Enter post title: '
  title = STDIN.gets.chomp
  abort 'No title.' unless title.length > 0

  print "Enter post categories(default: dev): "
  categories = STDIN.gets.chomp
  if categories.length == 0
    categories = 'dev'
  end

  title = Pinyin.t title
  filename = "_posts/#{Time.new.strftime('%Y-%m-%d')}-#{title.downcase.gsub(' ', '-')}.markdown"
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end

  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
      post.puts "---"
      post.puts "layout: post"
      post.puts "title: #{title}"
      post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
      post.puts "categories: [#{categories}]"
      post.puts "tags: []"
      post.puts "publish: true"
      post.puts "---"
      post.puts ""
  end
  # sh "open #{filename}"
end

desc 'Build site with Jekyll'
task :build do
  sh 'jekyll build'
end

desc "list tasks"
task :list do
  puts "Tasks: #{(Rake::Task.tasks - [Rake::Task[:list]]).join(', ')}"
end

