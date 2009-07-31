DATA_ROOT = File.dirname(__FILE__) + "/data"

namespace :algo2 do
  file "main_lang.txt" => ["data/lang.txt"] do 
    File.open("main_lang.txt", "w") do |o|
      File.open("data/lang.txt") do |f|
        f.each_line do |l|
          repo, stats = l.strip.split(":")

          pairs = stats.split(",")
          pairs.map! do |pair|
            lang, loc = pair.split(";")
            [ lang, loc.to_i ]
          end

          pairs = pairs.sort { |a,b| a[1] <=> b[1] }.reverse
          o.write "#{repo}:#{pairs.first.first}\n"
        end
      end
    end
  end

  file "sorted_langs.txt" => ["main_lang.txt"] do
    users_hash = {}
    repos_hash = {}
    langs_hash = {}
    sorted_langs_hash = {}

    File.open("data/data.txt") do |f|
      f.each_line do |l|
        user, repo = l.strip.split(":").map {|i| i.to_i }
        users_hash[user] ||= []
        users_hash[user] << repo
        repos_hash[repo] ||= []
        repos_hash[repo] << user
      end
    end
    
    File.open("main_lang.txt") do |f|
      f.each_line do |l|
        repo, lang = l.strip.split(":")
        repo = repo.to_i
        langs_hash[lang] ||= []
        langs_hash[lang] << repo
      end
    end
    
    langs_hash.each_pair do |lang, repos|
      repos = repos.reject {|r| repos_hash[r].nil? }
      sorted_langs_hash[lang] = repos.sort {|a,b| repos_hash[a].size <=> repos_hash[b].size }.reverse
    end
    
    File.open("sorted_langs.txt", "w") do |o|
      sorted_langs_hash.each_pair do |lang, repos|
        o.write "#{lang}:#{repos.join(',')}\n"
      end
    end
    
  end
end