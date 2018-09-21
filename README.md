# InstaMod
## An Automoderator-like bot which allows moderators to create a custom rule set based on a user's account history

### InstaMod is capable of automatically assigning users' flair, advanced thread locking, subreddit-wide filtering, creating a subreddit progression system, and more. It can also work in conjunction with AutoMod via user flair text. Below I will describe the different rule types and give an example of how some of them work.

-----
### Automatic User Flair:
Features, such as the subreddit progression system, can be displayed in a user's flair and keep it continuously updated. These flairs consist of a serries of tags that moderators can design rules for. Everything is able to be customized from the provided configuration file. Each rule can use one data point from this list of possible user information:
* Total comment karma/total post karma/total karma
* Comment/Post karma borken down by subreddit
* Positive/Negative posts/comments broken down by subreddit
* Comment count based on a custom comment filter

### Subreddit Tags:
Moderators can provide lists of subreddits which the bot will collect information on. This information can be used to create tags for users' flair. The sub tag's rules can be set to display a user's top 3 most used communities from a list of related subreddits. It could also be designed to show all the subreddits where a user has been consistently downvoted. Here are some of this tag's unique characteristics:
* Abbreviations for subreddit names
* Grouping subreddits
* Showing the top/bottom X number of subreddits that match a rule

### Subreddit Progression:
As a user participates more and more in the community, their flair can change to represent their involvement. Certain tags, or levels of user participation, can grant the user access to special priveledges. This includes the ability to assign themself custom flair and the ability to append their automatic flair with a designated list of :images:. For instance, you could give every user with less than 25 positive comments in your subreddit a tag that says "New Here". While every user with over 100 positive comments gets to overwrite their automatic flair.

### Etc Tags:
In case that wasn't enough, I have an Ace up my sleeve. Tags in this section allow you to replace the configuration file's rule settings and inject code directly into the program. This allows for the highest level of customization, but also requires knowledge of Python and a somewhat good understanding of the documentation. You are essentially writing the block of code that assigns the tag manually, instead of using the configuration file to define the tag.

### Advanced Thread/Sub Locking:
Traditional thread locking is all-or-nothing and AutoModerator can only filter users' comments based on flair text and account age. With InstaMod, a post's comment section can be filtered through a rule. If a user doesn't meet the requirements, then their comment can either be removed or marked spam. There is also a way to automatically notify the user of their comment's deletion. Moderators simply have to assign a post a specific flair for it to be locked. This type of rule can also be applied to the subreddit as a whole, and is activated/deactivated via PM.

-----
# Configuration File Documentation
Here I will describe each element of the provided config file, list the possible options for each setting, and provide some example cases

### SUB_CONFIG: Primary settings for toggling on/off features, as well as other meta options
* **name** - Name of the subreddit InstaMod is running on
* **abbrev** - Abbreviation of the subreddit
* **mods** - Comma seperated list () of usernames. Each username shold be encased in ''
* **thread_lock** to **ub_tags** - Accepts True/False values to turn on/off the feature
* **tag_expiration** - Number of days until a user's flair is reassessed 
* **accnt_age** - Represents number of months. If an account is younger than this, then they will get a tag for it. Change it to None to disable
* **update_interval** - set to 'INSTANT' for users to be analyzed as they are seen, or set it to any number <= 500 to flair users in batches. Once the stored lis of users is >= this number, they will be flaired
* **approved_icons** - a comma seperated list () of flair :images: that users with 'FLAIR_ICONS' permission will be able to use.

### QC_CONFIG: Filtered comment counter
Comments with values >= both of these numbers count as 1 positive QC
* **positive karma** - comments must have a score thats >= this number
* **positive words** - comments must contain an amount of words >= this number
Comments with values <= both of these numbers count as 1 negative QC
* **negative karma** - comments must have a score thats <= this number
* **negative words** - comments must contain an amount of words <= this number
-----
## Options for different rule settings: 
Each term must be used inbetween '' and typed the same way

### metric: user data used for rule definition
* **total comment karma**, **total post karma**, **total karma** - Self explanatory. These values are for across all subreddits and will ignore the 'target_sub' rule setting. They will not be totaled like other data points.
* **comment karma** and **post karma** - Total comment/post karma by subreddit
 **positive comment** and **negative comment** - Count of comments with a score < 0 or > 0 by subreddit
* **positive post** and **negative post** - Count of posts with a score < 0 or > 0 by subreddit
* **positive QC**, **negative QC**, **net QC** - The number of QC by subreddit. net_QC_counter consists of the negative QC subtracted from the positive QC by subreddit

### target_subs: subreddits the metric is pulled from
* A_SUBS/B_SUBS - Moderators can define 2 lists of subreddits. Each subreddit in a list must be accompanied by an abbreviation/nickname. Subreddits with identical abbreviations will be grouped together and have their data combined.
* ALL_SUBS - A combination of A_SUBS and B_SUBS
* ('OneSub', 'AnotherSub', 'AndSomeMore,) - Subreddits can be listed inside the parentheses to create a sub-list. These subreddits must already exist in either A_SUBS or B_SUBS.

### comparison: comparison between the metric and the defined value
* LESS_THAN - <
* GREATER_THAN - >
* EQUAL_TO - =
* NOT_EQUAL_TO - !=
* GREATER_THAN_EQUAL_TO - >=
* LESS_THAN_EQUAL_TO - <=

### value: the value used in conjunction with the metric and the comparison
* this can be any integer value

### flair_text: the flair text assigned when the rule requirements are met
* this can be any string text

### flair_css: CSS class the text user's flair will be assigned
* this must be already defined in the subreddit settings

### permissions: granting the user extra control over their flair if they meet the rule requirements
* FLAIR_IMAGES - The user will be able to append their flair with a selection of :image: flair's that the subreddit has already defined. Available :images: can be set using the approved_icons list in the sub_config option
* CUSTOM_FLAIR - The user can assign themself custom flair and overwrite the automatic flair. User's must have control of their flair revoked for this bot to effectively flair users.

## Defining a rule
Wow you made it this far. I'm honestly impressed. Lucky for you, this is where it gets juicy. Let's take a look at a sample rule and see if we can make sense of it:

'tier1' : {'metric' : 'positive comments', 
				'target_subs' : ('A_SUBS'), 
				'comparison' : 'LESS_THAN_EQUAL_TO', 
				'value' : 20, 
				'flair_text' : 'New Arrival', 
				'flair_css' : None, 
				'permissions' : None},
        
I speek nerd so allow me to translate: If a user has 0 - 20 positive comments in subreddits from A_SUBS, then they get the tag "New Arrival" in their flair text. See that wasn't so hard right?! The configuration file comes with a few examples of the different types of rules and their "translations".

Did you catch that? I said "different types of rules"...did you really think I was going to make it THAT easy for you? Each rule type has mostly the same set of options. Below we will go over each rule type, their new settings, and give an example.

### PROGRESS_CONFIG:
This is the same type that was used in the example above. A user can only be assigned 1 of the tags in this set of rules, so that they represent different stages of subreddit progression. This is also the onyl rule set that can grant users permissions. For instance, here is the next example in the set of example progression tags:

'tier2' : {'metric' : 'positive comments', 
				'target_subs' : ('A_SUBS'), 
				'comparison' : 'LESS_THAN_EQUAL_TO', 
				'value' : 50, 
				'flair_text' : 'Been Here', 
				'flair_css' : None, 
				'permissions' : None},
        
This one reads almost the same. The only thing that's changed is the value option. Since only one of these tiers can be selected, the rule reads as: If a user has 21 - 50 positive comments in subreddits from A_SUBS, then they get the tag "Been Here" in their flair text

### SUBTAG_CONFIG:
These tags are applied for each subreddit (individually) in the target_subs setting. That means that their metrics are not totaled like in the previous section. Before I show you an example, we've got some more key terms to go over class. Take out your pen, and get something to write on:
* sort - This can be set to MOST_COMMON, LEAST_COMMON, or None. Most common sorts the results from high to low, least common sorts them from low to high, and None just returns them in the order they are listed.
* tag_cap - The maximum number of subs that will be listed as a result of the rule
* pre_text and post_text - These strings are appended to the subreddit abbreviation

Since you've stuck with me this far, here are 2 examples:

'subtag1' : {'metric' : 'net QC', 
				'target_subs' : ('A_SUBS'), 
				'sort' : 'MOST_COMMON', 
				'tag_cap' : 3, 
				'comparison' : 'GREATER_THAN_EQUAL_TO', 
				'value' : 15, 
				'pre_text' : 'r/', 
				'post_text' : ''},
				
	'subtag2' : {'metric' : 'net QC', 
				'target_subs' : ('A_SUBS'), 
				'sort' : 'LEAST_COMMON', 
				'tag_cap' : 3, 
				'comparison' : 'LESS_THAN_EQUAL_TO', 
				'value' : -10, 
				'pre_text' : 'Trolls r/', 
				'post_text' : ''}
      
The first rule tags users with 15 or higher net QC with the text "r/SomeSub", while the second rule tags users with -10 or less net QC with the text "Trolls r/SomeSub". Where "SomeSub" is the given abbreviation for a subreddit.

### THREADLOCK_CONFIG:
InstaMod will scan for posts with specific post flair and filter the comments under it. A comment can either be removed or marked as spam. The bot can also notify users of their comment's removal. Same as the last one, we've got some new key terms:
* flair_ID - The post flair text that the bot looks for
* action - This can be set to "REMOVE" to remove the comment, or "SPAM" to mark the comment as spam
* remove_message - Message that will be sent to the user if their comment is removed. Set to None to not reply

Ok now that that's out of the way, more examples! Here is a thread lock that filters out the same users who would have the "New Arrival" tag that we previously defined:

'threadlock1' : {'metric' : 'positive comments', 
					'target_subs' : ('CM'), 
					'comparison' : 'LESS_THAN_EQUAL_TO', 
					'value' : 20, 
					'flair_ID' : 'lvl 1 Lock', 
					'action' : 'REMOVE'},

Translation: If a user has 20 or less positive comments, remove only their comments under this post
-----
##Well folks, believe it or not that's all I've got for now. I'll add more to this later as more features come online, but until then, try and get creative with these settings! There is a ton of stuff you can do with this bot, and I'm sure there are even more I haven't though of. If you have any ideas of what to add, code suggestions, etc, please feel free to shoot me a message on Reddit: /u/shimmyjimmy97
