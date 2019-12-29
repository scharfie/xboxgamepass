require 'bundler'
Bundler.require

task :list do
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

  names = details.body["Products"].map do |p|
    name = p["LocalizedProperties"][0]["ProductTitle"]
  end

  puts names
end
