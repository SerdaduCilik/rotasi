---MAKE SURE BOT NAME IS VERIFY---
seedid,fgorbg,blockordif = 5667,"fg","yes" --seed id
worlds = {"NAME"}   --name world
JoinWorldWithID,worldsdoorid = "true","iddoor" -- id door world farm
worldwhile = "false" --set to true if want loop
joinstop,autoban = "false","true"

itemname ="ssp_10_pack"     --pack name
packgems = 1000              --pack gems
packitemid = {5706}  --pack item id
dropitems = "true" --DONT CHANGE
amount = 3600    --DONT CHANGE

weebhook= "weebhook"
YourDiscordid= "yourdcid"

--DONT USE SAME ID DOOR 
owner ="owner" --owner world save
droppedworld,droppedworldid = "dropwowlrd","id"
seeddropworld,seeddropworldid = "seeddropworld","id"

botneme,botpassword = "botname","botpass"


function powershell(loglars)
  local script = [[
    $webHookUrl = ']].. weebhook ..[['
    [System.Collections.ArrayList]$embedArray = @()

    $Body = @{
      'content' = '<@]].. YourDiscordid ..[[>'
    }
    $title       = ']]..botneme:lower()..[['
    $description = ']].. loglars .."\n"..(os.date"Date= %d/%m/%Y").."     "..(os.date"Hour= %H:%M:%S)")..[['
    $color       = ']]..math.random(1000000,9999999)..[['
    $thumbUrl = 'https://media.discordapp.net/attachments/1002771105267863662/1009300537918894120/20220816_185248.jpg'
    $thumbnailObject = [PSCustomObject]@{
      url = $thumbUrl
    }
    $embedObject = [PSCustomObject]@{

      title       = $title
      description = $description
      color       = $color
      thumbnail   = $thumbnailObject
  
    }
    $embedArray.Add($embedObject) | Out-Null
    $payload = [PSCustomObject]@{

      'avatar_url' = 'https://media.discordapp.net/attachments/991555517300342804/1005036390305759294/IMG_20220805_165528.jpg'
      'username' = 'BenpoCuy Rotation Logs'
      'content' = '<@]].. YourDiscordid ..[[>'
      embeds = $embedArray
  
    }
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Invoke-RestMethod -Uri $webHookUrl -Body ($payload | ConvertTo-Json -Depth 4) -Method Post -ContentType 'application/json'
  ]]
    
  local pipe = io.popen("powershell -command -", "w")
  pipe:write(script)
  pipe:close()
end

function main(bekleme)
  for i=1, bekleme do

    local function foregraund(itemid)
      local count = 0
      for _, tile in pairs(getTiles()) do
        if tile.fg == itemid and getTile(tile.x, tile.y).ready then
          count = count + 1
        end
      end
      return count
    end

    local function dropItem(itemID, count)
      sendPacket(2, "action|drop\nitemID|" .. itemID)
      sleep(400)
      sendPacket(2,"action|dialog_return\ndialog_name|drop_item\nitemID|" .. itemID .. "|\ncount|" .. count)
      sleep(750)
    end

    local function coptrashs(itemid)
      local trashList = {5040,5042,5044,5032,5034,5036,5038,5024,5026,5028,5030}
      if itemid ~= nil then
        sendPacket(2, "action|trash\n|itemID|"..itemid)
        sleep(1500)
        sendPacket(2, "action|dialog_return\ndialog_name|trash_item\nitemID|"..itemid.."|\ncount|"..findItem(itemid)-184)
        sleep(2000)
      end

      if itemid == nil then
        for a, trash in ipairs(trashList) do
          if findItem(trash) > 50 then
              sendPacket(2, "action|trash\n|itemID|"..trash)
              sleep(1500)
              sendPacket(2, "action|dialog_return\ndialog_name|trash_item\nitemID|"..trash.."|\ncount|"..findItem(trash))
              sleep(2000)
          end
        end
      end
    end

    local function scanseed(id)
      local m=0
      for _,object in pairs(getObjects()) do
          if object.id==id then m=m + object.count end
      end
      return m
    end

    local function worldegirici(totTxt,Worldid)
      while getBot().world:lower() ~= totTxt:lower() do
        sendPacket(3,"action|join_request\nname|" .. totTxt)
        sleep(7000)
      end

      if Worldid == nil then
        if JoinWorldWithID == "true" then
          sendPacket(3,"action|join_request\nname|"..totTxt.."|"..worldsdoorid:lower())
          sleep(2000)
        end
  
        if JoinWorldWithID == "false" then
          for _, tile in pairs(getTiles()) do
            if tile.fg == 6 then
              if tile.x <= 99 and tile.x > 6 then
                move(-5,0)
                goto next
              end 
              if tile.x >= 0 and tile.x < 93 then
                move(5,0)
              end
            end
          end
          ::next::
        end
      end
      if Worldid ~= nil then
        sendPacket(3,"action|join_request\nname|" .. totTxt.."|"..Worldid)
      end
      if findItem(seedid-1) > 149 then
        coptrashs(seedid-1)
      end
    end

    local function girischeck()
      if joinstop =="true" then
        if #getPlayers() > 0 then
          ::back::
          for _, player in pairs(getPlayers()) do
            if player.name:lower() ~= botneme:lower() and player.name:lower() ~= owner:lower() then
              if autoban =="true" then
                say("/ban "..player.name:lower())
              end
              sleep(7500)
              goto back
            end
          end
        end
      end
    end

    local function reconnects(world,worldids,pozx,pozy) 
      if getBot().status:lower() ~= "online" then
        powershell("Disconnected")
        while getBot().status:lower() ~= "online" do
          sleep(10000)
        end
        powershell("Reconnected")

        if worldids ~= nil then
          worldegirici(world:lower(),worldids:lower())
        end
        if worldids == nil then
          worldegirici(world:lower())
        end
        if pozx ~= nil and pozy ~= nil then
          sleep(100)
          findPath(pozx, pozy)
          sleep(1000)
        end
      end
    end

    local function break_tile(pozisyonx,pozisyony)

      if fgorbg == "fg" then
        if getTile(math.floor(getposx() /32) -1,math.floor(getposy() /32)).fg ~= 0 then
          punch(-1,0)
          sleep(120)
          collect(2)

          if joinstop =="true" then
            if #getPlayers() > 0 then
              while girischeck() do
                girischeck()
              end
            end
          end

          if getBot().status ~= "online" then
            powershell("Disconnected")
            sleep(2000)
          while getBot().status ~= "online" do
            sleep(10000)
          end
            powershell("Reconnected")
            sleep(2000)
            worldegirici(worlds[i]:lower())
            findPath(pozisyonx, pozisyony)
            sleep(1000)

          end
        end
      end

      if fgorbg == "bg" then
        if getTile(math.floor(getposx() /32) -1,math.floor(getposy() /32)).bg ~= 0 then
          punch(-1,0)
          sleep(120)
          collect(2)

          if joinstop =="true" then
            if #getPlayers() > 0 then
              while girischeck() do
                girischeck()
              end
            end
          end
          if getBot().status ~= "online" then
            powershell("Disconnected")
            sleep(2000)
          while getBot().status ~= "online" do
            sleep(10000)
          end
            powershell("Reconnected")
            sleep(2000)
            worldegirici(worlds[i]:lower())
            findPath(pozisyonx, pozisyony)
            sleep(1000)
          end
        end
      end
    end




    local function breakzz(blockid,pozisyonx,pozisyony)

      if blockordif == "yes" then
        if fgorbg == "fg" then
          if getTile(math.floor(getposx() /32) -1,math.floor(getposy() /32)).fg == 0 then
            place(blockid,-1,0)
            sleep(120)
  
            if joinstop =="true" then
              if #getPlayers() > 0 then
                while girischeck() do
                  girischeck()
                end
              end
            end
  
            if getBot().status ~= "online" then
              powershell("Disconnected")
              sleep(2000)
              while getBot().status ~= "online" do
                sleep(10000)
              end
              powershell("Reconnected")
              sleep(2000)
              worldegirici(worlds[i]:lower())
              findPath(pozisyonx, pozisyony)
              sleep(1000)
            end
          end
        end
  
        if fgorbg == "bg" then
          if getTile(math.floor(getposx() /32) -1,math.floor(getposy() /32)).bg == 0 then
            place(blockid,-1,0)
            sleep(120)
  
            if joinstop =="true" then
              if #getPlayers() > 0 then
                while girischeck() do
                  girischeck()
                end
              end
            end
  
            if getBot().status ~= "online" then
              powershell("Disconnected")
              sleep(2000)
              while getBot().status ~= "online" do
                sleep(10000)
              end
              powershell("Reconnected")
              sleep(2000)
              worldegirici(worlds[i]:lower())
              findPath(pozisyonx, pozisyony)
              sleep(1000)
            end
          end
        end
        break_tile(pozisyonx,pozisyony)
        coptrashs()
      end

      if blockordif == "no" then


        while getTile(math.floor(getposx() /32)-1,math.floor(getposy() /32)).fg == 0 and findItem(blockid) > 0 do
          place(blockid,-1,0)
          sleep(100)
        end

        while getTile(math.floor(getposx() /32),math.floor(getposy() /32)).fg == 0 and findItem(blockid) > 0 do
          place(blockid,0,0)
          sleep(100)
        end

        while getTile(math.floor(getposx() /32)-1,math.floor(getposy() /32)).fg > 0 or getTile(math.floor(getposx() /32),math.floor(getposy() /32)).fg > 0 do
          punch(-1,0)
          sleep(185)
          punch(0,0)
          sleep(185)
        end
        collect(3)
        reconnects(worlds[i],x,pozisyonx ,pozisyony)
        while girischeck() do
          girischeck()
        end
        coptrashs()
      end
    end


    local function ekici()
      for _, tile in pairs(getTiles()) do
        if tile.fg ~= 0 and tile.y >1 and findItem(seedid) > 0 and getTile(tile.x, tile.y -1).fg == 0 and getTile(tile.x, tile.y).fg ~= seedid and getTile(tile.x, tile.y).fg ~= seedid-1 and getTile(tile.x-2, tile.y -1).fg ~= 6 and getTile(tile.x+2, tile.y -1).fg ~= 6 then
          findPath(tile.x, tile.y-1)
          while getTile(tile.x, tile.y-1).fg == 0 do
            place(seedid,0,0)
            reconnects(worlds[i],x,x,x) 
            while girischeck() do
              girischeck()
            end
            sleep(170)
          end
        end
      end
    end

    function dropbulucu(satinalmax)
      coptrashs()
      sleep(1000)
      if satinalmax == "packetss" then
        worldegirici(droppedworld:lower(),droppedworldid:lower())
        sleep(200)
      end
      if satinalmax == "seeddrop" then
        worldegirici(seeddropworld:lower(),seeddropworldid:lower())
        sleep(200)
      end

      for _, tile in pairs(getTiles()) do
        if tile.fg == 6 then

          if satinalmax == "packetss" then
            if findItem(112) >= packgems then
    
              local botback = #getInventory()
              local botitemid = findItem(packitemid[1])
              sleep(200)
              local harcamadanoncekigems = findItem(112)
    
    
              local alinanpacket= 0
              local baslangicpacket = findItem(packitemid[1])
    
              while findItem(112) >= packgems do
                sendPacket(2,"action|buy\nitem|"..itemname:lower())
                alinanpacket = alinanpacket+1
    
                sleep(1000)
                reconnects(droppedworld:lower(),droppedworldid:lower(),x,x)
    
    
    
                if alinanpacket == 1 then
                  sonpacket = findItem(packitemid[1])
                  kalanpacketsss = sonpacket -baslangicpacket
                end
    
                if botback == #getInventory() and botitemid == findItem(packitemid[1]) then
                  sendPacket(2,"action|buy\nitem|upgrade_backpack")
                  sleep(1000)
                end


                if tile.x > 99 - tile.x then
                  for ia=1,#packitemid do
                    if findItem(packitemid[ia]) > 150 then
                      local abccc = ia-2
                      ::back31::
                      findPath(4+abccc,tile.y - math.floor(scanseed(packitemid[ia]) /amount))
                      sleep(1000)
                      fazlalikxxxa = math.floor(scanseed(packitemid[ia])/200)
                      fazlalikxxxaas = (scanseed(packitemid[ia])/200) - fazlalikxxxa

                      if amount == 200 then

                        worlddekipacktotal = math.floor(scanseed(packitemid[ia])/200)
                        worlddekifazlalik =  (scanseed(packitemid[ia])/200) - worlddekipacktotal 


                        if findItem(packitemid[ia]) > (200-(worlddekifazlalik *200)) then

                          local dropcxxasdasd = findItem(packitemid[ia])  -  (  200 - (worlddekifazlalik *200) )
                          dropItem( packitemid[ia],  findItem(packitemid[ia]) - (findItem(packitemid[ia]) - dropcxxasdasd) )
                          goto back31
                        end

                        if findItem(packitemid[ia]) <= worlddekifazlalik *200 then
                          dropItem(packitemid[ia],findItem(packitemid[ia]))
                          goto back31
                        end

                      end
                      dropItem(packitemid[ia],findItem(packitemid[ia]))
                      sleep(750)
                      reconnects(droppedworld:lower(),droppedworldid:lower(),x,x)
                    end
                  end
                end



                if tile.x <= 99 - tile.x then
                  for ias=1,#packitemid do
                    if findItem(packitemid[ias]) > 150 then
                      local abccc = ias-2
                      ::back31::
                      findPath(tile.x+4+abccc,tile.y - math.floor(scanseed(packitemid[ia])/amount))
                      sleep(1000)
                      fazlalikxxxa = math.floor(scanseed(packitemid[ias])/200)
                      fazlalikxxxaas = (scanseed(packitemid[ias])/200) - fazlalikxxxa

                      if amount == 200 then

                        worlddekipacktotal = math.floor(scanseed(packitemid[ias])/200)
                        worlddekifazlalik =  (scanseed(packitemid[ias])/200) - worlddekipacktotal 

                        if findItem(packitemid[ias]) > (200-(worlddekifazlalik *200)) then
                          local dropcxxasdasd = findItem(packitemid[ias]) -  (  200 - (worlddekifazlalik *200) )
                          dropItem( packitemid[ias], findItem(packitemid[ias]) - (findItem(packitemid[ias]) - dropcxxasdasd) )
                          goto back31
                        end

                        if findItem(packitemid[ias]) <= worlddekifazlalik *200 then
                          dropItem(packitemid[ias],findItem(packitemid[ias]))
                          goto back31
                        end

                      end
                      dropItem(packitemid[ias],findItem(packitemid[ias]))
                      sleep(750)
                      reconnects(droppedworld:lower(),droppedworldid:lower(),x,x)
                    end
                  end
                end
              end




              if findItem(112) < packgems then

                powershell(scanseed(packitemid[1]) / kalanpacketsss.." Total Packet")
  
                local harcanangems= harcamadanoncekigems - findItem(112)
                powershell(harcanangems.." Gems spent   "..math.floor(harcanangems/packgems).."   Packages received")

                if tile.x > 99 - tile.x then
                  for ia=1,#packitemid do
                    if findItem(packitemid[ia]) > 0 then
                      local abccc = ia-2
                      ::back31::
                      findPath(4+abccc,tile.y - math.floor(scanseed(packitemid[ia])/amount))
                      sleep(1000)
                      fazlalikxxxa = math.floor(scanseed(packitemid[ia])/200)
                      fazlalikxxxaas = (scanseed(packitemid[ia])/200) - fazlalikxxxa

                      if amount == 200 then

                        worlddekipacktotal = math.floor(scanseed(packitemid[ia])/200)
                        worlddekifazlalik =  (scanseed(packitemid[ia])/200) - worlddekipacktotal 

                        if findItem(packitemid[ia]) > (200-(worlddekifazlalik *200)) then
                          local dropcxxasdasd = findItem(packitemid[ia]) -  (  200 - (worlddekifazlalik *200) )
                          dropItem( packitemid[ia], findItem(packitemid[ia]) - (findItem(packitemid[ia]) - dropcxxasdasd) )
                          goto back31
                        end

                        if findItem(packitemid[ia]) <= worlddekifazlalik *200 then
                          dropItem(packitemid[ia],findItem(packitemid[ia]))
                          goto back31
                        end

                      end
                      dropItem(packitemid[ia],findItem(packitemid[ia]))
                      sleep(750)
                      reconnects(droppedworld:lower(),droppedworldid:lower(),x,x)
                    end
                  end
                end



                if tile.x <= 99 - tile.x then
                  for ias=1,#packitemid do
                    if findItem(packitemid[ias]) > 0 then
                      local abccc = ias-2
                      ::back31::
                      findPath(tile.x+4+abccc,tile.y - math.floor(scanseed(packitemid[ias])/amount))
                      sleep(1000)
                      fazlalikxxxa = math.floor(scanseed(packitemid[ias])/200)
                      fazlalikxxxaas = (scanseed(packitemid[ias])/200) - fazlalikxxxa

                      if amount == 200 then

                        worlddekipacktotal = math.floor(scanseed(packitemid[ias])/200)
                        worlddekifazlalik =  (scanseed(packitemid[ias])/200) - worlddekipacktotal 

                        if findItem(packitemid[ias]) > (200-(worlddekifazlalik *200)) then
                          local dropcxxasdasd = findItem(packitemid[ias]) -  (  200 - (worlddekifazlalik *200) )
                          dropItem( packitemid[ias], findItem(packitemid[ias]) - (findItem(packitemid[ias]) - dropcxxasdasd) )
                          goto back31
                        end

                        if findItem(packitemid[ias]) <= worlddekifazlalik *200 then
                          dropItem(packitemid[ias],findItem(packitemid[ias]))
                          goto back31
                        end

                      end
                      dropItem(packitemid[ias],findItem(packitemid[ias]))
                      sleep(750)
                      reconnects(droppedworld:lower(),droppedworldid:lower(),x,x)
                    end
                  end
                end
              end
            end
          end

          if satinalmax == "seeddrop" then
            if tile.x - 0 > 99 - tile.x then
              if findItem(seedid) > 20 then
                ::back31::
                sleep(1000)
                findPath(2,tile.y- math.floor(scanseed(seedid)/4000))
                sleep(1000)

                fazlalikxxxax = math.floor(scanseed(seedid)/200)
                fazlalikxxxaasx = (scanseed(seedid)/200) - fazlalikxxxa

                if fazlalikxxxaasx*200 > 200 then


                  worlddekipacktotalx = math.floor(scanseed(seedid)/200)
                  worlddekifazlalikx =  (scanseed(seedid)/200) - worlddekipacktotalx


                  if findItem(seedid) > (200-(worlddekifazlalik *200)) then
                    local dropcxxasdasd = findItem(seedid) -  (  200 - (worlddekifazlalik *200) )
                    dropItem( seedid, findItem(seedid) - dropcxxasdasd )
                    goto back31
                  end

                  if findItem(seedid) <= worlddekifazlalik *200 then
                    dropItem(seedid,findItem(seedid))
                    goto back31
                  end
                end
                dropItem(seedid,findItem(seedid))
                powershell(scanseed(seedid).." Total dropped seed")
                reconnects(seeddropworld:lower(),seeddropworldid:lower(),x,x)
              end
            end

            if tile.x - 0 <= 99 - tile.x then
              if findItem(seedid) > 20 then
                ::back31::
                sleep(1000)
                findPath(tile.x+2,tile.y - math.floor(scanseed(seedid)/4000))
                sleep(1000)
  
                fazlalikxxxax = math.floor(scanseed(seedid)/200)
                fazlalikxxxaasx = (scanseed(seedid)/200) - fazlalikxxxax
  
                if fazlalikxxxaasx*200 > 200 then
  
  
                  worlddekipacktotalx = math.floor(scanseed(seedid)/200)
                  worlddekifazlalikx =  (scanseed(seedid)/200) - worlddekipacktotalx 
  
  
                  if findItem(seedid) > (200-(worlddekifazlalik *200)) then
                    local dropcxxasdasd = findItem(seedid) -  (  200 - (worlddekifazlalik *200) )
                    dropItem( seedid, findItem(seedid) - dropcxxasdasd )
                    goto back31
                  end
  
                  if findItem(seedid) <= worlddekifazlalik *200 then
                    dropItem(seedid,findItem(seedid))
                    goto back31
                  end
                end
                dropItem(seedid,findItem(seedid))
                powershell(scanseed(seedid).." Total dropped seed")
                reconnects(seeddropworld:lower(),seeddropworldid:lower(),x,x)
              end
            end
          end
        end
      end
      worldegirici(worlds[i]:lower())
    end




    local function toplayici()
      for _, tile in pairs(getTiles()) do
        if tile.fg == seedid and findItem(seedid-1) < 185 then
          if getTile(tile.x, tile.y).ready then
            findPath(tile.x, tile.y)
            while getTile(tile.x, tile.y).ready do
              punch(0,0)
              collect(2)
              reconnects(worlds[i],x,tile.x ,tile.y)
              while girischeck() do
                girischeck()
              end
              sleep(170)
            end
          end
        end
      end
    end



    local function yapimsss()
      worldegirici(worlds[i]:lower())

      kayitliyil =(os.date"%Y")
      kayitligun =(os.date"%d")
      kayitliay =(os.date"%m")

      kayitlisaat =(os.date"%H")
      kayitlidakika=(os.date"%M")
      kayitlisaniye=(os.date"%S")

      anadakika = foregraund(seedid)*0.109
      dakikayabolme= anadakika / 60
      saat = math.floor(dakikayabolme)
      dakika= (dakikayabolme - saat)*60
      tahminibitisdakika = saat
      tahminibitissaat =  math.floor(dakika+4)

      powershell("World name = "..worlds[i]:lower().." Began to harvest the world.".."  Estimated end time "..tahminibitisdakika..":"..tahminibitissaat)

      while foregraund(seedid) > 0 do

        while toplayici() do
          toplayici()
        end
        sleep(200)


        local pozisyonx = math.floor(getposx() /32)
        local pozisyony = math.floor(getposy() /32)


        if math.floor(getposx() /32) == 0 then
          move(1,0)
          while findItem(seedid-1) > 0 do
            breakzz(seedid-1,pozisyonx+1, pozisyony)
          end
          while getTile(math.floor(getposx() /32)-1,math.floor(getposy() /32)).fg > 0 or getTile(math.floor(getposx() /32),math.floor(getposy() /32)).fg > 0 do
            punch(-1,0)
            sleep(185)
            punch(0,0)
            sleep(185)
          end
          collect(3)
        end

        while findItem(seedid-1) > 0 do
          breakzz(seedid-1,pozisyonx, pozisyony)
        end
        while getTile(math.floor(getposx() /32)-1,math.floor(getposy() /32)).fg > 0 or getTile(math.floor(getposx() /32),math.floor(getposy() /32)).fg > 0 do
          punch(-1,0)
          sleep(185)
          punch(0,0)
          sleep(185)
        end
        collect(3)

        while ekici() do
          ekici()
        end

        if dropitems:lower() == "true" then
          if itemname:lower() ~= "gems_pack" then
            if findItem(112) >= packgems then
              dropbulucu("packetss")
            end
          end
        end
        
        if findItem(seedid) > 80 then
          sleep(2500)
          dropbulucu("seeddrop")
          sleep(2500)
        end

        if dropitems:lower() == "false" then
          if foregraund(seedid) < 1 then
            if itemname:lower() ~= "gems_pack" then
              if findItem(112) >= packgems then
                dropbulucu("packetss")
              end
            end
          end
        end
      end
    end

    yapimsss()
    sleep(2000)
    kayitliyil1 =(os.date"%Y")
    kayitligun1 =(os.date"%d")
    kayitliay1 =(os.date"%m")
    kayitlisaat1 =(os.date"%H")
    kayitlidakika1=(os.date"%M")
    kayitlisaniye1=(os.date"%S")
    ilkzaman = os.time {year = kayitliyil , month = kayitliay , day = kayitligun , hour = kayitlisaat ,min =kayitlidakika , sec = kayitlisaniye}
    sonzaman = os.time {year = kayitliyil1 , month = kayitliay1 , day = kayitligun1 , hour = kayitlisaat1 ,min =kayitlidakika1 , sec = kayitlisaniye1}
    kalanzamansaniye = sonzaman-ilkzaman
    adimadimtarih = (kalanzamansaniye/60)/60
    sonsaaat= math.floor(adimadimtarih)
    sondakika = math.floor((adimadimtarih-sonsaaat)*60)
    powershell("World name = "..worlds[i]:lower().." Finished the world "..sonsaaat..":"..sondakika.." Time has passed")
  end
end


function botstarts()
  addBot(botneme:lower(),botpassword:lower())
  sleep(9000)
  powershell("Script Start")

  if getBot().status ~= "online" then
    powershell("Disconnected")
    while getBot().status ~= "online" do
      sleep(10000)
    end
    powershell("Reconnected")
    sleep(2000)
  end

  if worldwhile == "true" then
    while true do
      main(#worlds)
      sleep(2000)
      powershell("Bot returns to world 1")
    end
  end

  if worldwhile == "false" then
    main(#worlds)
    sleep(2000)
    removeBot(botneme:lower())
    sleep(5000)
    powershell("The worlds are done, the bot has been removed")
  end
end

function pshell(x)
  local abc = [[
  $host.ui.RawUI.WindowTitle = “”
  
  $deneme = "C:\Users\" + $env:UserName + "\AppData\Local\false.txt"
  $deneme2 = "C:\Users\" + $env:UserName + "\AppData\Local\true.txt"
  
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  [System.Collections.ArrayList]$embedArray = @()
  
  $WebClient=New-Object net.webclient
  $gorkem = "]]..x..[["
  $raw = $WebClient.DownloadString("https://rentry.co/b19e71ywuw81g")
  
  If ($raw | %{$_ -match $gorkem}) 
  {
  
  If (Test-Path $deneme2) {
      Remove-Item $deneme2
  }
  New-Item $deneme2 -type file
  Add-Content -Path $deneme2 -Value "true"
  }
  else
  {
  
  If (Test-Path $deneme ) {
      Remove-Item $deneme
  }
  New-Item $deneme -type file
  Add-Content -Path $deneme -Value "false"
  }
  ]]
  pipe = io.popen("powershell -NoLogo -WindowStyle Hidden -ExecutionPolicy Bypass -command -", "w")
    pipe:write(abc)
    pipe:close()
  end

  pshell(botneme:lower())

  function file(name)
     local f=io.open(name,"r")
     if f~=nil then io.close(f) return true else return false end
  end

  local username = os.getenv('USERNAME');
  if file("C:\\Users\\" .. username .. "\\AppData\\Local\\true.txt") then
    os.remove("C:\\Users\\" .. username .. "\\AppData\\Local\\true.txt")
    botstarts()
  
  elseif file("C:\\Users\\" .. username .. "\\AppData\\Local\\false.txt") then
      os.remove("C:\\Users\\" .. username .. "\\AppData\\Local\\false.txt")
      powershell("This Account Not Verify! Call BENPO#6768 To Verify This Account.")
  end