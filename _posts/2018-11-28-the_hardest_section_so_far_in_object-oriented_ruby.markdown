---
layout: post
title:      "The hardest section so far in Object-Oriented Ruby"
date:       2018-11-29 01:50:52 +0000
permalink:  the_hardest_section_so_far_in_object-oriented_ruby
---


Object Relationships is a big section with many difficult labs. This section comes right after Object Labs. I thought I had a solid understanding of Class and the instances and instance as well as classsmethods within the Class. However, learning to collaborating between objects (or Class) challenges my understanding on another level. 

The first Lab had me stuck more than 8 hours was Collaborating Objects Lab. It is test-driven lab with helpful hints of method contruction in the README file of IDE. However, some of the specs spelling out each method were different from the methods suggested in the README which has me confused. One of the biggest stumbling blocks were constructing Song#artist_name=(name). Here is the rspec test for it:

```
describe '#artist=' do
    it 'sets the artist object to belong to the song' do
      new_artist_object = Artist.new('King of Pop')
      song.artist = new_artist_object
      expect(song.artist).to eq(new_artist_object)
    end
  end
```

It shows no mention of how to incorporate the #artist_name = (name). Here is my original code for it.

```
def artist=(name)
    self.artist = Artist.find_or_create_by_name(name)
    artist.add_song(self)
end
```

I interpreted as is on the spec, that I needed to set a #artist=  to an artist instance. If an artist instance is found, use that instance or else create a new artist instance. Thenm  The bad thing was IDE threw a loop that could not be broken, even with Ctrl+C command to break the indefinite loop. IDE then threw an error message when I tried to restart learn.co website for the particular lab. This message showed up many times I tried to put in learn open command:

```
Looking for lesson...
Forking lesson...
Cloning lesson...
There was a problem cloning this lesson. Retrying...
Cloning lesson...
There was a problem cloning this lesson. Retrying...
Cloning lesson...
Cloning lesson...
Cannot clone this lesson right now. Please try again.
```

This problem was a pain because IDE would not start the lesson and fork it when I put in learn command. I had to have a technical coach submit a ticket on my behalf and the lesson was not available for coding for a week. Moreover, the github repository that had my old codes deleted all of my .rb files in the process and Learn.co support could not fix it from their end. I ended up going to that github repository settings and delete this repo(which only had README files at the time), and reforked the repo in Learn IDE. 

The big lesson learned is one can not name the same method and call on it in the body of the method (I defined #artist=(name) and then called .artist = on self (the song instance) in the body of the method). By changing the name of method mention above to artist_name=(name), the bug was fixed. I wished the instruction and the spec would have been more consistent with each other so that I was not confused on how to name my method. Here is the final code.

```
def artist_name=(name)
    self.artist = Artist.find_or_create_by_name(name)
    artist.add_song(self)
end
```


Another new method that I learn is the from the Class Dir. I did not code the #files right until I spoke to the technical coach via ScreenShare. 
```
def files
    @files = Dir.glob("#{path}/*.mp3").collect{|f| f.gsub("#{path}/", "")}
end
   
```

This means that I created an array of all .mp3 files in the path object (configured as a string under #Dir.glob) and then iterated this array and erased the path (#gsub) in each file's name with a ""(no space). In turn, each file will only contain the name of the file and not the entire path in which the file is in. 


