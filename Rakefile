require 'json'
require 'set'
# require 'git'

task :default do
  
  # data_repo = Git.open("xwing-data2")
  # data_repo.fetch
  # data_repo.pull
  
  # Update from git
  `cd xwing-data2 && git fetch origin master && git pull origin master`
  
  # Get flat list of all of the pilot files
  files = Dir["xwing-data2/data/pilots/**/*.json"]
  
  # Dictionary of xwsid to ship data
  ship_data = {}
  
  # Iterate through files
  files.each do |file|
    json = JSON.load(open(file))
    xws = json["xws"]
    
    # We need an xws id for each ship
    if xws.nil? || xws == 0
      puts "#{file} has no xws"
      next
    end
    
    ship = ship_data[xws]
    
    if ship
      # Update factions if we've already encountared this ship
      curr_factions = ship["faction"]
      new_factions = get_faction_set(json)
      
      ship["faction"] = new_factions.merge(curr_factions.to_set).to_a
    else
      # Remove unnecessary data
      json.delete("pilots")
      
      # Ensure faction is an array
      json["faction"] = get_faction_set(json).to_a
      
      # Add ship
      ship_data[xws] = json
    end
  end
  
  open("ships.json", "w") { |f|
    f.puts JSON.pretty_generate(ship_data)
  }
end

def get_faction_set(ship_json)
  faction = ship_json["faction"]
  
  if faction.kind_of?(Array)
    return faction.to_set
  elsif faction.kind_of?(String)
    return Set[faction]
  end
end

desc "Default: generate ships.json from pilot/ship data in xwing-data2 repo."
