---
layout: post
title:      "My first Flatiron Project: CLI- Program Scraper"
date:       2018-10-01 10:49:08 -0400
permalink:  the_hardest_section_so_far_for_me
---

By the time I started writing this blog, I have spent the total of 16 hours working by myself and with the section technical coaches to review this project. It was the first major project I had to do during FlatIron bootcamp and I am very proud of what I have accomplished. It was hard and tenuous and I managed to avoid it for 2 weeks, but the CLI project walkthrough videos put out by FlatIron founder Avi really helped me started. 

I decided to make my CLI project a scraper of [online programs](http://sinclair.edu/academics/online/) offered by Sinclair Community College, a local college where I have had classes. I also studied the Student Scraper lab carefully and modelled my files after [this lab](https://github.com/taisaoko/oo-student-scraper-v-000). The instruction also suggested link to [World's Best Restaurants](https://github.com/cjbrock/worlds-best-restaurants-cli-gem). I studied it and I found the codes very helpful. 

Two things I found most challenging in this project was scraping and structural organization of my lib files. Creating my CLI.rb was easy because it was the first step. It allows me to substitute in data (using #puts) that I would like to show once I successfully build my program:
```
puts <<-DOC.gsub /^\s*/,''

1. Business Management - 30 credit hours
2. Business Transfer - 30 credit hours
3. Corrections -36 credit hours

DOC
```
The confusing part is to decide whether or not to have a separate Class Scraper (scraper.rb) or to combine a .scrape method within Class Program(defined in program.rb). Once I separate the files, I wanted to scrape all the details and code it within a #self.scrape_program class method within Class Scraper. Through many trials and errors and stopping myself from pulling my hair out, I came up with the following codes to scrape the details of each program from Sinclair. 
```
def self.scrape_program_page(url) 
    # Scrape Sinclair's individual program website and return more info (url)
    html = open(url) 
    doc = Nokogiri::HTML(html) 
    program_page = {}
         program_page[:name] = doc.css(".row h1").text  
         program_page[:degree_type] = doc.css(".row h5")[1].text
         program_page[:contact] = doc.css(".panel-body").text.split.join(" ")
         program_page[:division] = doc.css("a")[30].text #doc.css("a")[30].attributes.values[0].text
         program_page[:department] = doc.css("a")[31].text #doc.css("a")[30].attributes.values[0].text
         program_page[:about] = doc.css(".col-md-9.col-sm-8.col-12.content").children[10].text.split.join(" ")
         program_page[:outcomes] = doc.css("section.col-md-9.col-sm-8.col-12.content ul li").text
         if program_page[:contact] == ""
           program_page[:career_opportunity] = doc.css("p")[1].text
         else 
           program_page[:career_opportunity] = doc.css("p")[3].text
         end  
    binding.pry
    program_page
end

```

Throughout the process, I learned to familiarize myself with running my own CLI executive (bin) file and working with pry to scrape the information I needed (a helpful but often tricky tool to use in IRB). At this point, I had to decide how to incorporate each URL from each program seamlessly from a index page which lists all the program, so I created #self.scrape_index_page class method within Class Scraper (scraper.rb). 
```
def self.scrape_index_page
    # Scrape Sinclair website and return programs based on that data ("http://sinclair.edu/academics/online/")
    doc = Nokogiri::HTML(open("http://sinclair.edu/academics/online/"))
    doc.css(".col-xl-8.col-12 ul li")
 end
```

Because #self.scrape_program class method is too clunky, I decided to replace this method with #program_details, which iterate through the lists of program and return each program's URL. Each of these programs URLs will then be used to scrape  program details described the Class Program. Here is the #program_details of Class Scraper:
```
def program_details
    self.class.scrape_index_page[0...-1].each do |r|
      ProgramScraper::Program.new_from_index_page(r)
    end
 end
```

The last major step is to write Class Program. With each program created (#new), I want to transfer the data (name and URL) I scraped from the index page and iterated through that list. I define an #initialize to initialize each program with instance variables @name and @url. I then define  #doc to open each program URL. The rest was to copy and paste the old #self.scrape_program and tweaking the code into the corresponding instance method in my Class Program. Here is an excerpt of my code.
```
class ProgramScraper::Program
attr_accessor :name, :url, :degree_type, :contact, :division, :department, :description, :outcomes, :career_opportunity
  
  @@all = []
  
  def self.new_from_index_page(r)
    self.new(r.text,
      "https://www.sinclair.edu#{r.css("a").attribute("href").text}")
  end
  
  def initialize(name=nil, url=nil)
    @name = name
    @url = url
    @@all << self
  end
  
  def self.all
    @@all 
  end
  
  def doc
    @doc ||= Nokogiri::HTML(open(self.url))
  end
    
  def degree_type
    @degree_type ||= doc.css(".row h5")[1].text
  end
	
end
```

I'm really happy with this project. It gave me hope that with curiosity, tenacity and persistence to decode others' codes and to fix my own's codes, I will get better at Object-oriented Ruby programming one day at a time.




