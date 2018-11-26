require 'json'
require 'open-uri'
require 'set'
# require 'git'

$XWINGDATA_URL = "https://raw.githubusercontent.com/guidokessels/xwing-data2/master/"
$MANIFEST_PATH = "data/manifest.json"

task :default do
  
  # Dictionary of xwsid to ship data
  ship_data = {}
  
  # Get xwingdata2 manifest
  manifest = JSON.load(open($XWINGDATA_URL + $MANIFEST_PATH))
  pilots = manifest["pilots"]
  
  # Iterate through factions & pilots
  pilots.each do |faction_ship_paths|
    faction_ships = {}
    
    faction = faction_ship_paths["faction"]
    ship_paths = faction_ship_paths["ships"]
    
    ship_paths.each do |path|
      puts path
      ship = JSON.load(open($XWINGDATA_URL + path))
      xws = ship["xws"]
      
      # We need an xws id for each ship
      if xws.nil? || xws == 0
        puts "#{file} has no xws"
        next
      end
      
      # Remove unnecessary data
      ship.delete("pilots")
      
      # Add ship
      faction_ships[xws] = ship
    end
    
    # Add faction
    ship_data[faction] = faction_ships
  end
  
  # Iterate through files
  # files.each do |file|
  #   json = JSON.load(open(file))
  #   xws = json["xws"]
  # 
  #   # We need an xws id for each ship
  #   if xws.nil? || xws == 0
  #     puts "#{file} has no xws"
  #     next
  #   end
  # 
  #   ship = ship_data[xws]
  # 
  #   if ship
  #     # Update factions if we've already encountared this ship
  #     curr_factions = ship["faction"]
  #     new_factions = get_faction_set(json)
  # 
  #     ship["faction"] = new_factions.merge(curr_factions.to_set).to_a
  #   else
  #     # Remove unnecessary data
  #     json.delete("pilots")
  # 
  #     # Ensure faction is an array
  #     json["faction"] = get_faction_set(json).to_a
  # 
  #     # Add ship
  #     ship_data[xws] = json
  #   end
  # end
  
  # Save to file
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

def get_ship_manifest(manifest_json)
  manifest_by_faction = {}
  
  factions = manifest_json["pilots"]
  factions.each do |json|
    faction = json["faction"]
    ships = json["ships"]
    manifest_by_faction[faction] = ships
  end
end

desc "Default: generate ships.json from pilot/ship data in xwing-data2 repo."
