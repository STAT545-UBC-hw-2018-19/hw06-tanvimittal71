---
title: "Data wrangling wrap up"
output: 
  html_document:
    keep_md: true
    toc: true
    theme: united
---



#### Load Packages


```r
suppressPackageStartupMessages(library(tidyverse))
library(repurrrsive)
library(listviewer)
library(knitr)
```

### Task 1: Character data

#### _String Basics_

**In code that doesn’t use stringr, you’ll often see paste() and paste0(). What’s the difference between the two functions? What stringr function are they equivalent to? How do the functions differ in their handling of NA?**

Let us first see the difference between paste() and paste0()


```r
fruits<-c("apple","banana","pear",NA)
color<-c("Red","Yellow","Green",NA)
paste(color,fruits,collapse = ",")
```

```
## [1] "Red apple,Yellow banana,Green pear,NA NA"
```

```r
paste0(color,fruits,collapse = ",")
```

```
## [1] "Redapple,Yellowbanana,Greenpear,NANA"
```

Difference between two functions:
paste0() has a default value for sep="" and for paste() the default is " ". 

The equivalent stringr function is str_c:


```r
str_c(color,fruits)
```

```
## [1] "Redapple"     "Yellowbanana" "Greenpear"    NA
```

*NA Handling*

As we can see paste() and paste0() both coerce NA into character and concatenate it with other strings.However, str_c() does not coerce NA into character and collapsing the data will give NA which makes more sense in practical applications.


```r
str_c(color,fruits,collapse = ",")
```

```
## [1] NA
```

**In your own words, describe the difference between the sep and collapse arguments to str_c()**


```r
hero1 <- c("superman","hulk","venom")
hero2 <- c("batman","thor","spiderman")
str_c(hero1,hero2,sep = "-",collapse = ",")
```

```
## [1] "superman-batman,hulk-thor,venom-spiderman"
```

+ sep: This argument is used to separate elements of strings concatenated using str_c()
+ collapse: This argument is used to separate different elements of a character vector while converting it into a single string

**Use str_length() and str_sub() to extract the middle character from a string. What will you do if the string has an even number of characters?**


```r
cities <- c("Vancouver","Montreal","Toronto")
#calculate middle index for strings
middle_c <- ceiling(str_length(cities)/2) #ceiling function rounds up the number
str_sub(cities,middle_c,middle_c)
```

```
## [1] "o" "t" "o"
```

**What does str_wrap() do? When might you want to use it?**

str_wrap() is used to re-format the data by adjusting width and indentation, as it can be seen above.


```r
text <- c("But I must explain to you how all this mistaken idea of denouncing pleasure and praising pain was born and I will give you a complete account of the system, and expound the actual teachings of the great explorer of the truth, the master-builder of human happiness.")
text
```

```
## [1] "But I must explain to you how all this mistaken idea of denouncing pleasure and praising pain was born and I will give you a complete account of the system, and expound the actual teachings of the great explorer of the truth, the master-builder of human happiness."
```

```r
cat(str_wrap(text,width = 50))
```

```
## But I must explain to you how all this mistaken
## idea of denouncing pleasure and praising pain was
## born and I will give you a complete account of the
## system, and expound the actual teachings of the
## great explorer of the truth, the master-builder of
## human happiness.
```

**What does str_trim() do? What’s the opposite of str_trim()?**

str_trim() is used to remove white spaces from the start and end of a string


```r
sentence <- c("  This is an example sentence    ")
trimmed.sentence <- str_trim(sentence) #str_squish() can be used to repeated spaces inside srings
```

The opposite of str_trim() is str_pad()


```r
str_pad(trimmed.sentence, width = 35, side = "both", pad = " " ) # any another character can be used in pad argument
```

```
## [1] "    This is an example sentence    "
```

**Write a function that turns (e.g.) a vector c("a", "b", "c") into the string a, b, and c. Think carefully about what it should do if given a vector of length 0, 1, or 2.**


```r
vect_to_string <- function(vect){
  #error for vectors of length 0
  if (length(vect) == 0) {
        stop("Enter a vector of length greater than 0")
  }
  #otherwise return string by using collapse in str_c()
  return(str_c(vect,collapse = ","))
}
input <- c("a","b","c")
vect_to_string(input)
```

```
## [1] "a,b,c"
```


#### _Matching patterns with regular expressions_

**Explain why each of these strings don’t match a \\: "\\", "\\\\", "\\\\\\".**

* "\\": escapes the next character in string.
* "\\\\": escapes the next character in regexp.
* "\\\\\\": first two backslashes escape literal \\ which escapes the next character

**How would you match the sequence "'\\?**


```r
x <- c("a\"\'\\b") #input string
str_view(x,"\"\'\\\\") # \ is used as escape character
```

<!--html_preserve--><div id="htmlwidget-b196224677c812682e6a" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-b196224677c812682e6a">{"x":{"html":"<ul>\n  <li>a<span class='match'>\"'\\<\/span>b<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

**What patterns will the regular expression \\..\\..\\.. match? How would you represent it as a string?**

As "\\" is not an escape character in regexp. We will use "\\\\" which will match .(character).(character).(character), for example .a.b.c


```r
x <- c("abcd","a.b.c.d")
str_view(x,"\\..\\..\\..")
```

<!--html_preserve--><div id="htmlwidget-de3e05de7c1e9279d6bd" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-de3e05de7c1e9279d6bd">{"x":{"html":"<ul>\n  <li>abcd<\/li>\n  <li>a<span class='match'>.b.c.d<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

**How would you match the literal string "\$^\$"?**


```r
x <-c("$^$","$^$abc","abc$^$")
str_view(x,"^\\$\\^\\$$")
```

<!--html_preserve--><div id="htmlwidget-f924420597fc72151e57" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-f924420597fc72151e57">{"x":{"html":"<ul>\n  <li><span class='match'>$^$<\/span><\/li>\n  <li>$^$abc<\/li>\n  <li>abc$^$<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

**Given the corpus of common words in stringr::words, create regular expressions that find all words that:**

  1. Start with “y”.
  2. End with “x”
  3. Are exactly three letters long. (Don’t cheat by using str_length()!)
  4. Have seven letters or more.
  

```r
# ^y: starts with y
# x$: ends with x
# ^...$: exactly three letters long
# .......: seven letters or more
str_subset(stringr::words,"^y|x$|^...$|.......")
```

```
##   [1] "absolute"    "account"     "achieve"     "act"         "add"        
##   [6] "address"     "advertise"   "afternoon"   "against"     "age"        
##  [11] "ago"         "air"         "all"         "already"     "alright"    
##  [16] "although"    "america"     "and"         "another"     "any"        
##  [21] "apparent"    "appoint"     "approach"    "appropriate" "arm"        
##  [26] "arrange"     "art"         "ask"         "associate"   "authority"  
##  [31] "available"   "bad"         "bag"         "balance"     "bar"        
##  [36] "because"     "bed"         "believe"     "benefit"     "bet"        
##  [41] "between"     "big"         "bit"         "box"         "boy"        
##  [46] "brilliant"   "britain"     "brother"     "bus"         "business"   
##  [51] "but"         "buy"         "can"         "car"         "cat"        
##  [56] "certain"     "chairman"    "character"   "Christmas"   "colleague"  
##  [61] "collect"     "college"     "comment"     "committee"   "community"  
##  [66] "company"     "compare"     "complete"    "compute"     "concern"    
##  [71] "condition"   "consider"    "consult"     "contact"     "continue"   
##  [76] "contract"    "control"     "converse"    "correct"     "council"    
##  [81] "country"     "cup"         "current"     "cut"         "dad"        
##  [86] "day"         "decision"    "definite"    "department"  "describe"   
##  [91] "develop"     "die"         "difference"  "difficult"   "discuss"    
##  [96] "district"    "document"    "dog"         "dry"         "due"        
## [101] "eat"         "economy"     "educate"     "egg"         "electric"   
## [106] "encourage"   "end"         "english"     "environment" "especial"   
## [111] "evening"     "evidence"    "example"     "exercise"    "expense"    
## [116] "experience"  "explain"     "express"     "eye"         "far"        
## [121] "few"         "finance"     "fit"         "fly"         "for"        
## [126] "fortune"     "forward"     "fun"         "function"    "further"    
## [131] "gas"         "general"     "germany"     "get"         "god"        
## [136] "goodbye"     "guy"         "history"     "hit"         "holiday"    
## [141] "hospital"    "hot"         "how"         "however"     "hundred"    
## [146] "husband"     "identify"    "imagine"     "important"   "improve"    
## [151] "include"     "increase"    "individual"  "industry"    "instead"    
## [156] "interest"    "introduce"   "involve"     "job"         "key"        
## [161] "kid"         "kitchen"     "lad"         "language"    "law"        
## [166] "lay"         "leg"         "let"         "lie"         "lot"        
## [171] "low"         "machine"     "man"         "may"         "meaning"    
## [176] "measure"     "mention"     "million"     "minister"    "morning"    
## [181] "mrs"         "necessary"   "new"         "non"         "not"        
## [186] "now"         "obvious"     "occasion"    "odd"         "off"        
## [191] "old"         "one"         "operate"     "opportunity" "organize"   
## [196] "original"    "otherwise"   "out"         "own"         "paragraph"  
## [201] "particular"  "pay"         "pension"     "per"         "percent"    
## [206] "perfect"     "perhaps"     "photograph"  "picture"     "politic"    
## [211] "position"    "positive"    "possible"    "practise"    "prepare"    
## [216] "present"     "pressure"    "presume"     "previous"    "private"    
## [221] "probable"    "problem"     "proceed"     "process"     "produce"    
## [226] "product"     "programme"   "project"     "propose"     "protect"    
## [231] "provide"     "purpose"     "put"         "quality"     "quarter"    
## [236] "question"    "realise"     "receive"     "recognize"   "recommend"  
## [241] "red"         "relation"    "remember"    "represent"   "require"    
## [246] "research"    "resource"    "respect"     "responsible" "rid"        
## [251] "run"         "saturday"    "say"         "science"     "scotland"   
## [256] "secretary"   "section"     "see"         "separate"    "serious"    
## [261] "service"     "set"         "sex"         "she"         "similar"    
## [266] "sir"         "sit"         "situate"     "six"         "society"    
## [271] "son"         "special"     "specific"    "standard"    "station"    
## [276] "straight"    "strategy"    "structure"   "student"     "subject"    
## [281] "succeed"     "suggest"     "sun"         "support"     "suppose"    
## [286] "surprise"    "tax"         "tea"         "telephone"   "television" 
## [291] "ten"         "terrible"    "the"         "therefore"   "thirteen"   
## [296] "thousand"    "through"     "thursday"    "tie"         "together"   
## [301] "tomorrow"    "tonight"     "too"         "top"         "traffic"    
## [306] "transport"   "trouble"     "try"         "tuesday"     "two"        
## [311] "understand"  "university"  "use"         "various"     "village"    
## [316] "war"         "way"         "wednesday"   "wee"         "welcome"    
## [321] "whether"     "who"         "why"         "win"         "without"    
## [326] "year"        "yes"         "yesterday"   "yet"         "you"        
## [331] "young"
```


**Create regular expressions to find all words that:**

* Start with a vowel.


```r
total_words <- stringr::words
str_subset(total_words,"^[aeiou]")
```

```
##   [1] "a"           "able"        "about"       "absolute"    "accept"     
##   [6] "account"     "achieve"     "across"      "act"         "active"     
##  [11] "actual"      "add"         "address"     "admit"       "advertise"  
##  [16] "affect"      "afford"      "after"       "afternoon"   "again"      
##  [21] "against"     "age"         "agent"       "ago"         "agree"      
##  [26] "air"         "all"         "allow"       "almost"      "along"      
##  [31] "already"     "alright"     "also"        "although"    "always"     
##  [36] "america"     "amount"      "and"         "another"     "answer"     
##  [41] "any"         "apart"       "apparent"    "appear"      "apply"      
##  [46] "appoint"     "approach"    "appropriate" "area"        "argue"      
##  [51] "arm"         "around"      "arrange"     "art"         "as"         
##  [56] "ask"         "associate"   "assume"      "at"          "attend"     
##  [61] "authority"   "available"   "aware"       "away"        "awful"      
##  [66] "each"        "early"       "east"        "easy"        "eat"        
##  [71] "economy"     "educate"     "effect"      "egg"         "eight"      
##  [76] "either"      "elect"       "electric"    "eleven"      "else"       
##  [81] "employ"      "encourage"   "end"         "engine"      "english"    
##  [86] "enjoy"       "enough"      "enter"       "environment" "equal"      
##  [91] "especial"    "europe"      "even"        "evening"     "ever"       
##  [96] "every"       "evidence"    "exact"       "example"     "except"     
## [101] "excuse"      "exercise"    "exist"       "expect"      "expense"    
## [106] "experience"  "explain"     "express"     "extra"       "eye"        
## [111] "idea"        "identify"    "if"          "imagine"     "important"  
## [116] "improve"     "in"          "include"     "income"      "increase"   
## [121] "indeed"      "individual"  "industry"    "inform"      "inside"     
## [126] "instead"     "insure"      "interest"    "into"        "introduce"  
## [131] "invest"      "involve"     "issue"       "it"          "item"       
## [136] "obvious"     "occasion"    "odd"         "of"          "off"        
## [141] "offer"       "office"      "often"       "okay"        "old"        
## [146] "on"          "once"        "one"         "only"        "open"       
## [151] "operate"     "opportunity" "oppose"      "or"          "order"      
## [156] "organize"    "original"    "other"       "otherwise"   "ought"      
## [161] "out"         "over"        "own"         "under"       "understand" 
## [166] "union"       "unit"        "unite"       "university"  "unless"     
## [171] "until"       "up"          "upon"        "use"         "usual"
```


* That only contain consonants. (Hint: thinking about matching “not”-vowels.)


```r
str_subset(total_words,"^[^aeiou]+$")
```

```
## [1] "by"  "dry" "fly" "mrs" "try" "why"
```

* End with ed, but not with eed.


```r
str_subset(total_words,"[^e]ed$")
```

```
## [1] "bed"     "hundred" "red"
```

* End with ing or ise.


```r
str_subset(total_words,"ing$|ise$")
```

```
##  [1] "advertise" "bring"     "during"    "evening"   "exercise" 
##  [6] "king"      "meaning"   "morning"   "otherwise" "practise" 
## [11] "raise"     "realise"   "ring"      "rise"      "sing"     
## [16] "surprise"  "thing"
```


**Empirically verify the rule “i before e except after c”.**


```r
str_subset(total_words,"[^c]ie|cei") #words following the rule
```

```
##  [1] "achieve"    "believe"    "brief"      "client"     "die"       
##  [6] "experience" "field"      "friend"     "lie"        "piece"     
## [11] "quiet"      "receive"    "tie"        "view"
```

```r
str_subset(total_words,"[^c]ei")# words where the rule is not followed
```

```
## [1] "weigh"
```

**Is “q” always followed by a “u”?**

Yes, "q" is always followed by a "u".


```r
str_subset(total_words,"q[^u]")#checking words with "q" not followed by a "u"
```

```
## character(0)
```


**Create a regular expression that will match telephone numbers as commonly written in your country.**


```r
phone_no <- c("123-456-7890","12345-67890","1234567890","1234")
str_subset(phone_no,"\\d{3}-\\d{3}-\\d{4}")# checking format xxx-xxx-xxxx
```

```
## [1] "123-456-7890"
```


**Describe the equivalents of ?, +, * in {m,n} form.**

* \?:\{0,1\}
* \+:\{1,\}
* \*:\{0,\}

**Describe in words what these regular expressions match: (read carefully to see if I’m using a regular expression or a string that defines a regular expression.)**

  1. ^.*$: starts with 0 or more character. Matches all the strings, even an empty one.
  2. "\\{.+\\}": matches the strings containing one or more character in curly braces, example: a{b}c
  3. \d{4}-\d{2}-\d{2}: throws an error, as it should be \\\\d instead of \\d to match a digit
  4. "\\\\{4}": matches strings with four backslahes.


```r
a <- c("abc","123","a{a}3","a\\\\\\\\b")
str_view(a,"^.*$")
```

<!--html_preserve--><div id="htmlwidget-cbc3cd1a98569f73213e" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-cbc3cd1a98569f73213e">{"x":{"html":"<ul>\n  <li><span class='match'>abc<\/span><\/li>\n  <li><span class='match'>123<\/span><\/li>\n  <li><span class='match'>a{a}3<\/span><\/li>\n  <li><span class='match'>a\\\\\\\\b<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
str_view(a,"\\{.+\\}")
```

<!--html_preserve--><div id="htmlwidget-1c9af1dc7095000edf01" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-1c9af1dc7095000edf01">{"x":{"html":"<ul>\n  <li>abc<\/li>\n  <li>123<\/li>\n  <li>a<span class='match'>{a}<\/span>3<\/li>\n  <li>a\\\\\\\\b<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
#str_view(a,"\d{4}-\d{2}-\d{2}") #commented to avoid the error
str_view(a,"\\\\{4}")
```

<!--html_preserve--><div id="htmlwidget-725d1d54c26635e893eb" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-725d1d54c26635e893eb">{"x":{"html":"<ul>\n  <li>abc<\/li>\n  <li>123<\/li>\n  <li>a{a}3<\/li>\n  <li>a<span class='match'>\\\\\\\\<\/span>b<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

**Create regular expressions to find all words that:**

  1. Start with three consonants.
  

```r
str_subset(total_words,"^[^aeiou]{3}")
```

```
##  [1] "Christ"    "Christmas" "dry"       "fly"       "mrs"      
##  [6] "scheme"    "school"    "straight"  "strategy"  "street"   
## [11] "strike"    "strong"    "structure" "system"    "three"    
## [16] "through"   "throw"     "try"       "type"      "why"
```
  
  2. Have three or more vowels in a row.
  

```r
str_subset(total_words,"[aeiou]{3}")
```

```
## [1] "beauty"   "obvious"  "previous" "quiet"    "serious"  "various"
```
  
  3. Have two or more vowel-consonant pairs in a row.
  

```r
str_subset(total_words,"([aeiou][^aeiou]){2,}|([^aeiou][aeiou]){2,}")
```

```
##   [1] "absolute"    "active"      "advertise"   "agent"       "along"      
##   [6] "america"     "another"     "apart"       "apparent"    "associate"  
##  [11] "assume"      "authority"   "available"   "aware"       "away"       
##  [16] "balance"     "base"        "basis"       "because"     "become"     
##  [21] "before"      "begin"       "behind"      "believe"     "benefit"    
##  [26] "bloke"       "britain"     "business"    "cake"        "care"       
##  [31] "case"        "character"   "close"       "closes"      "college"    
##  [36] "colour"      "come"        "community"   "compare"     "complete"   
##  [41] "compute"     "condition"   "consider"    "continue"    "cover"      
##  [46] "date"        "debate"      "decide"      "decision"    "definite"   
##  [51] "department"  "depend"      "describe"    "design"      "detail"     
##  [56] "develop"     "difference"  "difficult"   "direct"      "divide"     
##  [61] "document"    "drive"       "during"      "economy"     "educate"    
##  [66] "elect"       "electric"    "eleven"      "encourage"   "engine"     
##  [71] "environment" "especial"    "europe"      "even"        "evening"    
##  [76] "ever"        "every"       "evidence"    "exact"       "example"    
##  [81] "excuse"      "exercise"    "exist"       "experience"  "face"       
##  [86] "family"      "favour"      "figure"      "file"        "final"      
##  [91] "finance"     "fine"        "finish"      "fire"        "five"       
##  [96] "fortune"     "friday"      "future"      "game"        "general"    
## [101] "give"        "govern"      "hate"        "have"        "here"       
## [106] "holiday"     "home"        "honest"      "hope"        "hospital"   
## [111] "however"     "identify"    "imagine"     "improve"     "include"    
## [116] "income"      "individual"  "inside"      "insure"      "interest"   
## [121] "introduce"   "item"        "jesus"       "labour"      "late"       
## [126] "level"       "life"        "like"        "likely"      "limit"      
## [131] "line"        "live"        "local"       "lose"        "love"       
## [136] "machine"     "major"       "make"        "manage"      "meaning"    
## [141] "measure"     "mile"        "minister"    "minus"       "minute"     
## [146] "moment"      "money"       "more"        "motion"      "move"       
## [151] "music"       "name"        "nation"      "nature"      "necessary"  
## [156] "never"       "nice"        "nine"        "none"        "note"       
## [161] "notice"      "occasion"    "office"      "okay"        "open"       
## [166] "operate"     "opportunity" "oppose"      "organize"    "original"   
## [171] "otherwise"   "over"        "page"        "paper"       "paragraph"  
## [176] "parent"      "particular"  "period"      "photograph"  "picture"    
## [181] "place"       "police"      "policy"      "politic"     "position"   
## [186] "positive"    "power"       "practise"    "prepare"     "present"    
## [191] "pressure"    "presume"     "previous"    "price"       "private"    
## [196] "probable"    "proceed"     "process"     "produce"     "product"    
## [201] "project"     "proper"      "propose"     "protect"     "provide"    
## [206] "purpose"     "quality"     "radio"       "rate"        "realise"    
## [211] "reason"      "receive"     "recent"      "recognize"   "recommend"  
## [216] "record"      "reduce"      "refer"       "regard"      "region"     
## [221] "relation"    "remember"    "report"      "represent"   "require"    
## [226] "research"    "resource"    "result"      "return"      "rise"       
## [231] "role"        "rule"        "safe"        "sale"        "same"       
## [236] "saturday"    "save"        "scheme"      "score"       "second"     
## [241] "secretary"   "secure"      "separate"    "serious"     "service"    
## [246] "seven"       "share"       "side"        "similar"     "site"       
## [251] "situate"     "size"        "smoke"       "social"      "society"    
## [256] "some"        "space"       "special"     "specific"    "stage"      
## [261] "state"       "station"     "strategy"    "strike"      "structure"  
## [266] "student"     "stupid"      "suppose"     "sure"        "surprise"   
## [271] "take"        "tape"        "telephone"   "television"  "there"      
## [276] "therefore"   "thousand"    "time"        "today"       "together"   
## [281] "tomorrow"    "tonight"     "total"       "toward"      "trade"      
## [286] "travel"      "unit"        "unite"       "university"  "upon"       
## [291] "value"       "various"     "video"       "village"     "visit"      
## [296] "vote"        "wage"        "water"       "welcome"     "where"      
## [301] "while"       "white"       "whole"       "wide"        "wife"       
## [306] "woman"       "write"
```


**Describe, in words, what these expressions will match:**

1. (.)\\\\1\\\\1: strings containing "xxx"
2. "(.)(.)\\\\2\\\\1": strings containing \"xyyx\"
3. (..)\\\\1: strings containing \"xyxy\"
4. "(.).\\\\1.\\\\1": strings containing \"x<char>x<char>x\"
5. "(.)(.)(.).*\\\\3\\\\2\\\\1": strings containing"xyz<char>zyx"


```r
words <- c("aaa","aabaa","abba","abab","abaca","abcdcba")
str_subset(words,"(.)\\1\\1")
```

```
## [1] "aaa"
```

```r
str_subset(words, "(.)(.)\\2\\1")
```

```
## [1] "abba"
```

```r
str_subset(words,"(..)\\1")
```

```
## [1] "abab"
```

```r
str_subset(words,"(.).\\1.\\1")
```

```
## [1] "abaca"
```

```r
str_subset(words,"(.)(.)(.).*\\3\\2\\1")
```

```
## [1] "abcdcba"
```

**Construct regular expressions to match words that:**

  1. Start and end with the same character.
  

```r
str_subset(total_words,"^(.).*\\1$")
```

```
##  [1] "america"    "area"       "dad"        "dead"       "depend"    
##  [6] "educate"    "else"       "encourage"  "engine"     "europe"    
## [11] "evidence"   "example"    "excuse"     "exercise"   "expense"   
## [16] "experience" "eye"        "health"     "high"       "knock"     
## [21] "level"      "local"      "nation"     "non"        "rather"    
## [26] "refer"      "remember"   "serious"    "stairs"     "test"      
## [31] "tonight"    "transport"  "treat"      "trust"      "window"    
## [36] "yesterday"
```

  2. Contain a repeated pair of letters (e.g. “church” contains “ch” repeated twice.)


```r
str_subset(total_words,"(..).*\\1")
```

```
##  [1] "appropriate" "church"      "condition"   "decide"      "environment"
##  [6] "london"      "paragraph"   "particular"  "photograph"  "prepare"    
## [11] "pressure"    "remember"    "represent"   "require"     "sense"      
## [16] "therefore"   "understand"  "whether"
```

  3. Contain one letter repeated in at least three places (e.g. “eleven” contains three “e”s.)


```r
str_subset(total_words,"(.).\\1.\\1.*$")
```

```
## [1] "eleven"
```

#### Tools

**For each of the following challenges, try solving it by using both a single regular expression, and a combination of multiple str_detect() calls.**

  1. Find all words that start or end with x
  

```r
str_subset(total_words,"^x|x$") #single regular expression
```

```
## [1] "box" "sex" "six" "tax"
```

```r
(start_x <- total_words[str_detect(total_words,"^x")])
```

```
## character(0)
```

```r
(end_x <- total_words[str_detect(total_words,"x$")])
```

```
## [1] "box" "sex" "six" "tax"
```

  2. Find all words that start with a vowel and end with a consonant.
  

```r
str_subset(total_words,"^[aeiou].*[^aeiou]$")
```

```
##   [1] "about"       "accept"      "account"     "across"      "act"        
##   [6] "actual"      "add"         "address"     "admit"       "affect"     
##  [11] "afford"      "after"       "afternoon"   "again"       "against"    
##  [16] "agent"       "air"         "all"         "allow"       "almost"     
##  [21] "along"       "already"     "alright"     "although"    "always"     
##  [26] "amount"      "and"         "another"     "answer"      "any"        
##  [31] "apart"       "apparent"    "appear"      "apply"       "appoint"    
##  [36] "approach"    "arm"         "around"      "art"         "as"         
##  [41] "ask"         "at"          "attend"      "authority"   "away"       
##  [46] "awful"       "each"        "early"       "east"        "easy"       
##  [51] "eat"         "economy"     "effect"      "egg"         "eight"      
##  [56] "either"      "elect"       "electric"    "eleven"      "employ"     
##  [61] "end"         "english"     "enjoy"       "enough"      "enter"      
##  [66] "environment" "equal"       "especial"    "even"        "evening"    
##  [71] "ever"        "every"       "exact"       "except"      "exist"      
##  [76] "expect"      "explain"     "express"     "identify"    "if"         
##  [81] "important"   "in"          "indeed"      "individual"  "industry"   
##  [86] "inform"      "instead"     "interest"    "invest"      "it"         
##  [91] "item"        "obvious"     "occasion"    "odd"         "of"         
##  [96] "off"         "offer"       "often"       "okay"        "old"        
## [101] "on"          "only"        "open"        "opportunity" "or"         
## [106] "order"       "original"    "other"       "ought"       "out"        
## [111] "over"        "own"         "under"       "understand"  "union"      
## [116] "unit"        "university"  "unless"      "until"       "up"         
## [121] "upon"        "usual"
```

```r
total_words[str_detect(total_words,"^[aeiou].*[^aeiou]$")]
```

```
##   [1] "about"       "accept"      "account"     "across"      "act"        
##   [6] "actual"      "add"         "address"     "admit"       "affect"     
##  [11] "afford"      "after"       "afternoon"   "again"       "against"    
##  [16] "agent"       "air"         "all"         "allow"       "almost"     
##  [21] "along"       "already"     "alright"     "although"    "always"     
##  [26] "amount"      "and"         "another"     "answer"      "any"        
##  [31] "apart"       "apparent"    "appear"      "apply"       "appoint"    
##  [36] "approach"    "arm"         "around"      "art"         "as"         
##  [41] "ask"         "at"          "attend"      "authority"   "away"       
##  [46] "awful"       "each"        "early"       "east"        "easy"       
##  [51] "eat"         "economy"     "effect"      "egg"         "eight"      
##  [56] "either"      "elect"       "electric"    "eleven"      "employ"     
##  [61] "end"         "english"     "enjoy"       "enough"      "enter"      
##  [66] "environment" "equal"       "especial"    "even"        "evening"    
##  [71] "ever"        "every"       "exact"       "except"      "exist"      
##  [76] "expect"      "explain"     "express"     "identify"    "if"         
##  [81] "important"   "in"          "indeed"      "individual"  "industry"   
##  [86] "inform"      "instead"     "interest"    "invest"      "it"         
##  [91] "item"        "obvious"     "occasion"    "odd"         "of"         
##  [96] "off"         "offer"       "often"       "okay"        "old"        
## [101] "on"          "only"        "open"        "opportunity" "or"         
## [106] "order"       "original"    "other"       "ought"       "out"        
## [111] "over"        "own"         "under"       "understand"  "union"      
## [116] "unit"        "university"  "unless"      "until"       "up"         
## [121] "upon"        "usual"
```

**In the previous example, you might have noticed that the regular expression matched “flickered”, which is not a colour. Modify the regex to fix the problem.**


```r
# creating colours group vector to match
colours <- c(" red", " orange", " yellow", " green", " blue", " purple")
colour_match <- str_c(colours, collapse = "|")
more <- sentences[str_count(sentences, colour_match) > 1]
str_view_all(more, colour_match)
```

<!--html_preserve--><div id="htmlwidget-baafda6dc525b7a303c6" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-baafda6dc525b7a303c6">{"x":{"html":"<ul>\n  <li>It is hard to erase<span class='match'> blue<\/span> or<span class='match'> red<\/span> ink.<\/li>\n  <li>The sky in the west is tinged with<span class='match'> orange<\/span><span class='match'> red<\/span>.<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

**From the Harvard sentences data, extract the first word from each sentence.**
  

```r
head(str_extract(sentences,"."))
```

```
## [1] "T" "G" "I" "T" "R" "T"
```



### Task 2: Work with a list

#### Simplifying data from a list of GitHub users

repurrsive package has 6 default Github users data included. Let us first observe the structure of data


```r
#jsonedit(gh_users, mode="view") # remove # to view data interactively
```

str() command can be used to view structure of data.


```r
str(gh_users, max.level = 1) #max.level is used to specify maximal level of nesting to be displayed
```

```
## List of 6
##  $ :List of 30
##  $ :List of 30
##  $ :List of 30
##  $ :List of 30
##  $ :List of 30
##  $ :List of 30
```

So, the data contains information of 6 users with nested 30 lists each.

Argument max.level is used to provide maximal level of nesting to be displayed. It is set to NA by default which means all levels of nesting to be displayed.

Another argument of str() is list.len which specifies maximum number of list elements to display within a level


```r
str(gh_users,max_level=2,list.len = 2)
```

```
## List of 6
##  $ :List of 30
##   ..$ login              : chr "gaborcsardi"
##   ..$ id                 : int 660288
##   .. [list output truncated]
##  $ :List of 30
##   ..$ login              : chr "jennybc"
##   ..$ id                 : int 599454
##   .. [list output truncated]
##   [list output truncated]
```

So, list.len truncates the list and nested list levels to be displayed.

Now, let us see truncated nested list of a particular user.


```r
str(gh_users[[1]],list.len = 8)
```

```
## List of 30
##  $ login              : chr "gaborcsardi"
##  $ id                 : int 660288
##  $ avatar_url         : chr "https://avatars.githubusercontent.com/u/660288?v=3"
##  $ gravatar_id        : chr ""
##  $ url                : chr "https://api.github.com/users/gaborcsardi"
##  $ html_url           : chr "https://github.com/gaborcsardi"
##  $ followers_url      : chr "https://api.github.com/users/gaborcsardi/followers"
##  $ following_url      : chr "https://api.github.com/users/gaborcsardi/following{/other_user}"
##   [list output truncated]
```

#### Inspect elements of a single user:

Inspecting elements 1, 2, 6, 18, 21, and 24 of the list component for the 5th GitHub user.


```r
sub_list <- c(gh_users[[5]][1],gh_users[[5]][2],gh_users[[5]][6],gh_users[[5]][18],gh_users[[5]][21],gh_users[[5]][24])
sub_list
```

```
## $login
## [1] "leeper"
## 
## $id
## [1] 3505428
## 
## $html_url
## [1] "https://github.com/leeper"
## 
## $name
## [1] "Thomas J. Leeper"
## 
## $location
## [1] "London, United Kingdom"
## 
## $bio
## [1] "Political scientist and R hacker. Interested in open science, public opinion research, surveys, experiments, crowdsourcing, and cloud computing."
```

#### Extract an element

To extract one element from each of the list we can use map() function
> The map function transforms the input by applying a function to each element, the first argument is input and second is the function to be applied.


```r
map(gh_users,"id") # it can also be extracted using position of the element
```

```
## [[1]]
## [1] 660288
## 
## [[2]]
## [1] 599454
## 
## [[3]]
## [1] 1571674
## 
## [[4]]
## [1] 12505835
## 
## [[5]]
## [1] 3505428
## 
## [[6]]
## [1] 8360597
```

As it can be seen above, map() returns a list. However, if we already know the expected type of output, map_type(example map_chr()) can be used to return atomic vector


```r
map_int(gh_users,2) #using position index for Id
```

```
## [1]   660288   599454  1571674 12505835  3505428  8360597
```

#### Extract multiple values


```r
map(gh_users,magrittr::extract,c("name","id","login","location")) #`[` can also be used in place of magrittr::extract
```

```
## [[1]]
## [[1]]$name
## [1] "Gábor Csárdi"
## 
## [[1]]$id
## [1] 660288
## 
## [[1]]$login
## [1] "gaborcsardi"
## 
## [[1]]$location
## [1] "Chippenham, UK"
## 
## 
## [[2]]
## [[2]]$name
## [1] "Jennifer (Jenny) Bryan"
## 
## [[2]]$id
## [1] 599454
## 
## [[2]]$login
## [1] "jennybc"
## 
## [[2]]$location
## [1] "Vancouver, BC, Canada"
## 
## 
## [[3]]
## [[3]]$name
## [1] "Jeff L."
## 
## [[3]]$id
## [1] 1571674
## 
## [[3]]$login
## [1] "jtleek"
## 
## [[3]]$location
## [1] "Baltimore,MD"
## 
## 
## [[4]]
## [[4]]$name
## [1] "Julia Silge"
## 
## [[4]]$id
## [1] 12505835
## 
## [[4]]$login
## [1] "juliasilge"
## 
## [[4]]$location
## [1] "Salt Lake City, UT"
## 
## 
## [[5]]
## [[5]]$name
## [1] "Thomas J. Leeper"
## 
## [[5]]$id
## [1] 3505428
## 
## [[5]]$login
## [1] "leeper"
## 
## [[5]]$location
## [1] "London, United Kingdom"
## 
## 
## [[6]]
## [[6]]$name
## [1] "Maëlle Salmon"
## 
## [[6]]$id
## [1] 8360597
## 
## [[6]]$login
## [1] "masalmon"
## 
## [[6]]$location
## [1] "Barcelona, Spain"
```

However the data above is still in form of nested list.

#### Extract data as a data frame


```r
map_df(gh_users,`[`,c("name","login","id","location")) %>%
  kable()
```



name                     login                id  location               
-----------------------  ------------  ---------  -----------------------
Gábor Csárdi             gaborcsardi      660288  Chippenham, UK         
Jennifer (Jenny) Bryan   jennybc          599454  Vancouver, BC, Canada  
Jeff L.                  jtleek          1571674  Baltimore,MD           
Julia Silge              juliasilge     12505835  Salt Lake City, UT     
Thomas J. Leeper         leeper          3505428  London, United Kingdom 
Maëlle Salmon            masalmon        8360597  Barcelona, Spain       

gh_users have one level of nesting. But in real life it is quite common to have more.

Let us explore a list of lists of lists, gh_repos:


```r
str(gh_repos,max.level = 2, list.len = 3)
```

```
## List of 6
##  $ :List of 30
##   ..$ :List of 68
##   ..$ :List of 68
##   ..$ :List of 68
##   .. [list output truncated]
##  $ :List of 30
##   ..$ :List of 68
##   ..$ :List of 68
##   ..$ :List of 68
##   .. [list output truncated]
##  $ :List of 30
##   ..$ :List of 68
##   ..$ :List of 68
##   ..$ :List of 68
##   .. [list output truncated]
##   [list output truncated]
```

It is data of 6 users and a list of their first 30 repos with further details.

#### Extract value in lists with more than single nesting

To extract the value in a list that has multiple levels we need to provide a vector input such that the j-th element of vector addresses the j-th level of the hierarchy


```r
gh_repos %>%
  map(c(1,4,1)) # extracts from first repo of each user, the first element of 4th element which is a list
```

```
## [[1]]
## [1] "gaborcsardi"
## 
## [[2]]
## [1] "jennybc"
## 
## [[3]]
## [1] "jtleek"
## 
## [[4]]
## [1] "juliasilge"
## 
## [[5]]
## [1] "leeper"
## 
## [[6]]
## [1] "masalmon"
```

