# GitHub Contest 2009 Entry

[algo]: http://www10.org/cdrom/papers/519/

Write an [Item-based Collaborative Filtering Recommendation Algorithm][algo]

# Algorithm 1

    @all_users.each do |user|
      user.repositories.each do |repo|
        # Create neighbors: other users that are watching the same repo
        user.neighbors += (repo.users - user) # Adding a user that is already a neighbor increments their "weight"
      end
    end

    @test_users.each do |user|
      neighbors = user.neighbors.sort.reverse
      guesses   = []
      neighbors.each do |neighbor|
        neighbor.repositories.sort {|a,b| a.users.size <=> b.users.size }.reverse.each do |repo|
          guesses << repo unless user.repositories.include?(repo)
        end
      end
      user.guesses = guesses[0,10]
    end
