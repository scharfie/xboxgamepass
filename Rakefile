require 'date'
require 'bundler/inline'

gemfile do
  source "https://rubygems.org"
  gem "faraday"
  gem "faraday_middleware"
end

class XboxGamePass
  class Game
    attr_accessor :name

    def initialize(h={})
      h.each { |k, v| send(:"#{k}=", v) }
    end
  end

  def self.games
    collection_url = "https://reco-public.rec.mp.microsoft.com/channels/Reco/v8.0/lists/collection/pcgaVTaz?itemTypes=Devices&DeviceFamily=Windows.Desktop&market=US&language=EN&count=500";
    detail_url = "https://displaycatalog.mp.microsoft.com/v7.0/products?bigIds=PRODUCT_IDS&market=US&languages=en-us&MS-CV=none";

    connection = Faraday.new do |b|
      b.response :json
      b.adapter Faraday.default_adapter
    end

    result = connection.get(collection_url)

    ids = result.body["Items"].map { |e| e["Id"] }

    detail_url = detail_url.sub("PRODUCT_IDS", ids.join(","))
    details = connection.get(detail_url)

    games = details.body["Products"].map do |p|
      Game.new(
        name: name = p["LocalizedProperties"][0]["ProductTitle"]
      )
    end

    return games
  end
end

task :default => :tsv

desc "Generates TSV list of games and copies result to clipboard"
task :tsv do
  result = ""
  games = XboxGamePass.games
  result << "Name\tLibrary\t(updated: #{Date.today})"
  games.each do |game|
    result << "\n%s\t%s" % [game.name, "Xbox Game Pass"]
  end

  IO.popen('pbcopy', 'w') { |f| f << result }
  puts result
end
