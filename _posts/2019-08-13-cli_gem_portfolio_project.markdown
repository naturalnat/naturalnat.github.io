---
layout: post
title:      "CLI Gem Portfolio Project "
date:       2019-08-13 02:12:28 -0400
permalink:  cli_gem_portfolio_project
---


The CLI Gem Portfolio Project was the first project of the program. It required building a CLI app that would have multiple layers of data and be interactive. My project provided a list of NYC Elected Offices. The menu allows the user to enter a number corresponding to an office to receive information on who the elected official is. This was inspired by my experience as an undergrad student, and working with policy in general - sometimes I just wished for a simple list of the elected officials who are most often mentioned/contacted. So I went from there.  

Beginning the project was intimidating. The walkthrough videos that were posted on the assignment page helped a lot. After getting the bin files set up and working, coding the rest of the methods that would display a menu and the results was not so bad -- I felt relatively confident after all of the lab work in coding those methods. 

The scraper was one of the more frustrating components of the lab to build and took a lot of trial and error to get working in order for it to be able to list the NYC 


website and list the Council Members. Other Officials, like the Borough Presidents and the Mayor, I hardcoded, so only current elected officials are retured. This is something I would like to expand on in the future, in addition to including more options for elected offices and information about them to be returned. 

edit: 
With some help from Flatiron instructors, I changed my scraper code and made it a little more complex. Instead of only listing the Council Members, I changed the code to provide the URL of their profile site. Additionally, I added another menu, which allows the user to select a Council Member to recieve more options on. This part was fun, because it requires a second scraper, which scrapes the profile pages of Council Members based on the user input. 


The first scraper, which gather a list of names and corresponding websites, creates an instance of a Council Member with their name and a URL:


    class Electedofficials::Scraper
      def self.scrape_cms
        page = Nokogiri::HTML(open('https://council.nyc.gov/districts/'))
        page.css('.sort-member').each do |member|
        name = member.css('a').text.strip
        url = member.css('a').first['href']

      Electedofficials::Official.new(name, url)
      end
     end
  


Because the URL was already scraped, I used that information in a second scraper. After a user is presented with a list of Council Members, the menu asks the user to input a number corresponding to the Council Member about which they'd like to learn more information. 


The Council Member menu looks like this: 

      def city_council
        city_council = Electedofficials::Official.all
        city_council.each.with_index(1) { |person, index| puts "#{index}. #{person.name} \n Profile Page: #{person.url}" }
        puts "Would you like more information on a Council Member? Type 'yes' for more info or 'exit' to exit"
        input = gets.strip.downcase
        if input == 'yes'
          puts 'Please enter the number corresponding to the CM to get more info'
          input = gets.strip
          city_council.each.with_index(1) do |person, index|
            if input == index.to_s
              more_info = Electedofficials::Scraper.scrape_cm(person.url)
              puts more_info
            end
          end
        end
      end



This menu takes an input of a string and, if it corresponds to the number of the Council Member, calls on the scraper to scrape the URL. 

This is the second scraper: 

      def self.scrape_cm(cm)
        page = Nokogiri::HTML(open(cm))
        bio = page.css('.page-content').text.strip
        d_office = page.css('.text-small').first.text.strip
        l_office = page.css('.text-small').last.text.strip
        email = page.css('.show-for-xlarge').text.strip
        puts "District Office:"
        puts d_office
        puts "\n"
        puts "Legislative Office"
        puts l_office
        puts "\n"
        puts email
        puts "\n"
        puts "Biography:"
        puts "\n"
        puts bio

      end
  
  
  
 This scrapes the individual profile pages of Council Members for their District and Legislative offices, as well as their biographies. This also provides their contact information. 


Overall, I am quite proud of what I have done, even though I recongize all its flaws and areas that could be improved. I never expected to have been doing this. 
