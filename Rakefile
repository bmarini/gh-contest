# ---------------------------------------------------------------------------
# Load repo_langs hash { :1 => [ ['Ruby',123],['Javascript',567] ] }
# ---------------------------------------------------------------------------
task :load_repo_langs => ["data/lang.txt"] do
  @repo_langs = {}

  File.open("data/lang.txt") do |f|
    f.each_line do |l|
      repo, stats = l.strip.split(":")

      pairs = stats.split(",")
      pairs.map! do |pair|
        lang, loc = pair.split(";")
        [ lang, loc.to_i ]
      end

      pairs = pairs.sort { |a,b| a[1] <=> b[1] }.reverse
      @repo_langs[repo.to_i] = pairs
    end
  end
end

# ---------------------------------------------------------------------------
# Generate file <repo>:<lang>
# ---------------------------------------------------------------------------
file "repo-lang.txt" => [:load_repo_langs] do 
  File.open("repo-lang.txt", "w") do |f|
    @repo_langs.each do |repo, pairs|
      f.puts "#{repo}:#{pairs.first.first}"
    end
  end
end

# ---------------------------------------------------------------------------
# Load user_repos and repo_users hashes
# ---------------------------------------------------------------------------
task :load_users_repos => ["data/data.txt"] do
  @user_repos = {}
  @repo_users = {}

  File.open("data/data.txt") do |f|
    f.each_line do |l|
      user, repo = l.strip.split(":").map {|i| i.to_i }
      @user_repos[user] ||= []
      @user_repos[user] << repo
      @repo_users[repo] ||= []
      @repo_users[repo] << user
    end
  end
end

# ---------------------------------------------------------------------------
# Load langs hash
# ---------------------------------------------------------------------------
task :load_lang_repos => ["repo-lang.txt"] do
  @lang_repos       = {}
  File.open("repo-lang.txt") do |f|
    f.each_line do |l|
      repo, lang = l.strip.split(":")
      repo = repo.to_i
      @lang_repos[lang] ||= []
      @lang_repos[lang] << repo
    end
  end
end

# ---------------------------------------------------------------------------
# For each language, most popular repos. Generate file: <lang>:<repo>,<repo>,...
# ---------------------------------------------------------------------------
file "lang-repos.txt" => ["repo-lang.txt", :load_lang_repos, :load_users_repos] do
  @sorted_lang_repos = {}
  
  @lang_repos.each_pair do |lang, repos|
    repos = repos.reject {|r| @repo_users[r].nil? }
    @sorted_lang_repos[lang] = repos.sort {|a,b| @repo_users[a].size <=> @repo_users[b].size }.reverse
  end
  
  File.open("lang-repos.txt", "w") do |f|
    @sorted_lang_repos.each_pair do |lang, repos|
      f.puts "#{lang}:#{repos.join(',')}"
    end
  end
end

# ---------------------------------------------------------------------------
# Generate file: <user>:<lang>
# ---------------------------------------------------------------------------
file "user-lang.txt" => [:load_repo_langs, :load_users_repos] do
  File.open("user-lang.txt", "w") do |f|
    @user_repos.each do |user, repos|
      langs = repos.collect {|r|
        @repo_langs[r].nil? ? nil : @repo_langs[r].first
      }.compact
      
      if langs.empty?
        f.puts "#{user}:Unknown"
      else
        main_lang = langs.sort {|a,b| a[1] <=> b[1] }.reverse.first.first
        f.puts "#{user}:#{main_lang}"
      end
    end
  end
end

task :load_user_lang => ["user-lang.txt"] do
  @user_lang = {}
  File.open("user-lang.txt") do |f|
    f.each_line do |l|
      user, lang = l.strip.split(":")
      @user_lang[user.to_i] = lang
    end
  end
end

task :load_test_users => ["data/test.txt"] do
  @test_users = []
  File.open("data/test.txt") do |f|
    f.each_line do |l|
      @test_users.push l.to_i
    end
  end
end

file "results.txt" => [:load_user_lang, :load_lang_repos, :load_users_repos, :load_test_users] do
  File.open("results.txt", "w") do |f|
    @test_users.each do |test_user|
      lang = @user_lang[test_user]

      if lang != "Unknown" && !@lang_repos[lang].nil?
        guesses = []
        @lang_repos[lang].each do |repo|
          guesses << repo unless @user_repos[test_user].include?(repo)
        end

        f.puts "#{test_user}:#{guesses[0,10].join(',')}"
      else
        puts "Skipping #{test_user}"
      end
    end
  end
end