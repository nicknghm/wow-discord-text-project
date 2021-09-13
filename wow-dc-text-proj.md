wow-dc-text-analysis
================
Nick
9/13/2021

## WoW with WOW discord text mining

This project’s main aim is to explore various text mining methods with
R, following along Dave Langer’s [Text Analytics with R
series](https://www.youtube.com/watch?v=4vuw0AsHeGw&list=PLTJTBoU5HOCR5Vkah2Z-AU76ZYsZjGFK6&ab_channel=DataScienceDojo)
here.

I will be exploring data from a World of Warcraft Q&A discord channel.

Have fun learning along with me!

## Required packages

``` r
library(tidyverse)
library(jsonlite)
library(lubridate)
```

## Getting the data

To get the discord data required for analysis, I used Tyrrrz’s
[DiscordChatExporter](https://github.com/Tyrrrz/DiscordChatExporter),
and pulled the data out from the `ask for help` channel from the r/WoW
Discord group.

Note that automating your Discord account is technically against
Discord’s TOS, so use with caution, and don’t pull out data too quickly
which might flag your account.

This channel is almost a Q&A of sorts, which could show trends in what
people are excited or need help the most with in World of Warcraft.

Data can be extracted in various output types, but we will be using JSON
to get the neatest dataset possible. Now, we’ll get the data into R
using the `jsonlite` package.

    ## List of 5
    ##  $ guild       :List of 3
    ##   ..$ id     : chr "113103747126747136"
    ##   ..$ name   : chr "World of Warcraft"
    ##   ..$ iconUrl: chr "https://cdn.discordapp.com/icons/113103747126747136/6848448806348c3cfc0c474707849a6a.png"
    ##  $ channel     :List of 6
    ##   ..$ id        : chr "205710841146900480"
    ##   ..$ type      : chr "GuildTextChat"
    ##   ..$ categoryId: chr "379819340029100042"
    ##   ..$ category  : chr "Serious Channels"
    ##   ..$ name      : chr "ask-for-help"
    ##   ..$ topic     : chr "**THIS IS NOT A CHANNEL FOR FINDING GROUPS.** If you are looking for in-game help, use the LFG channels or in-g"| __truncated__
    ##  $ dateRange   :List of 2
    ##   ..$ after : NULL
    ##   ..$ before: NULL
    ##  $ messages    :'data.frame':    707254 obs. of  13 variables:
    ##   ..$ id                : chr [1:707254] "205717885157507073" "205752140520488960" "205752390761054209" "205761208693948417" ...
    ##   ..$ type              : chr [1:707254] "Default" "Default" "Default" "Default" ...
    ##   ..$ timestamp         : chr [1:707254] "2016-07-21T16:09:25.875+00:00" "2016-07-21T18:25:32.99+00:00" "2016-07-21T18:26:32.652+00:00" "2016-07-21T19:01:35.011+00:00" ...
    ##   ..$ timestampEdited   : chr [1:707254] NA NA NA "2016-07-21T19:01:46.138+00:00" ...
    ##   ..$ callEndedTimestamp: logi [1:707254] NA NA NA NA NA NA ...
    ##   ..$ isPinned          : logi [1:707254] TRUE FALSE FALSE FALSE FALSE FALSE ...
    ##   ..$ content           : chr [1:707254] "Welcome to the Q&A channel. This is for simple Questions to be answered by the community away from the more inv"| __truncated__ "It's already out, enjoy! <U+0001F603>" "why is mw monk so bad now?" "MW monk being bad is kinda subjective honestly. Asking around in the Monk discord - they don't seem to think it"| __truncated__ ...
    ##   ..$ author            :'data.frame':   707254 obs. of  7 variables:
    ##   .. ..$ id           : chr [1:707254] "54660964481499136" "106429844627169280" "95323568774053888" "54396900614340608" ...
    ##   .. ..$ name         : chr [1:707254] "Coan" "Carl" "Noshei" "Shue" ...
    ##   .. ..$ discriminator: chr [1:707254] "0001" "0001" "0001" "3697" ...
    ##   .. ..$ nickname     : chr [1:707254] "Coan" "Carl [bot dev]" "Noshei" "Shue" ...
    ##   .. ..$ color        : chr [1:707254] NA "#F1C40F" NA NA ...
    ##   .. ..$ isBot        : logi [1:707254] FALSE FALSE FALSE FALSE FALSE FALSE ...
    ##   .. ..$ avatarUrl    : chr [1:707254] "https://cdn.discordapp.com/avatars/54660964481499136/62471158f6548215f81a48d431539ac3.png?size=128" "https://cdn.discordapp.com/avatars/106429844627169280/a_5981d65209d78b8bf28fc47fb1bb9336.gif?size=128" "https://cdn.discordapp.com/avatars/95323568774053888/075e4f9dfbea0ff7bbcbf18bb3e7ab2d.png?size=128" "https://cdn.discordapp.com/avatars/54396900614340608/ce5c8aa58c7d65b6e8496c209e6056b8.png?size=128" ...
    ##   ..$ attachments       :List of 707254
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. .. [list output truncated]
    ##   ..$ embeds            :List of 707254
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  1 obs. of  7 variables:
    ##   .. .. ..$ title      : chr "Legion Beta Top 5 Melee 1v1 Arena - Classes and Spec - Kings of ..."
    ##   .. .. ..$ url        : chr "https://www.youtube.com/watch?v=0CoAq132Otw"
    ##   .. .. ..$ timestamp  : logi NA
    ##   .. .. ..$ description: chr ""
    ##   .. .. ..$ author     :'data.frame':    1 obs. of  2 variables:
    ##   .. .. .. ..$ name: chr "Hazzed Gaming"
    ##   .. .. .. ..$ url : chr "https://www.youtube.com/user/darksword1337"
    ##   .. .. ..$ thumbnail  :'data.frame':    1 obs. of  3 variables:
    ##   .. .. .. ..$ url   : chr "https://images-ext-2.discordapp.net/external/tddkp_2dMJydzONQYxa9J7BglKMWD-se0iv0g3G_M6E/https/i.ytimg.com/vi/0"| __truncated__
    ##   .. .. .. ..$ width : int 480
    ##   .. .. .. ..$ height: int 360
    ##   .. .. ..$ fields     :List of 1
    ##   .. .. .. ..$ : list()
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  2 obs. of  6 variables:
    ##   .. .. ..$ title      : chr [1:2] "" ""
    ##   .. .. ..$ url        : chr [1:2] "https://realmpop.com/us.html" "http://i.imgur.com/Bx1RaJh.png"
    ##   .. .. ..$ timestamp  : logi [1:2] NA NA
    ##   .. .. ..$ description: chr [1:2] "" ""
    ##   .. .. ..$ fields     :List of 2
    ##   .. .. .. ..$ : list()
    ##   .. .. .. ..$ : list()
    ##   .. .. ..$ thumbnail  :'data.frame':    2 obs. of  3 variables:
    ##   .. .. .. ..$ url   : chr [1:2] NA "https://images-ext-1.discordapp.net/external/Hz5SE1yy8HvaC0pmeJktAMkIH7VSkI3Se1TnbQr5Zio/http/i.imgur.com/Bx1RaJh.png"
    ##   .. .. .. ..$ width : int [1:2] NA 884
    ##   .. .. .. ..$ height: int [1:2] NA 257
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. .. [list output truncated]
    ##   ..$ reactions         :List of 707254
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. .. [list output truncated]
    ##   ..$ mentions          :List of 707254
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  1 obs. of  5 variables:
    ##   .. .. ..$ id           : chr "205771140235264001"
    ##   .. .. ..$ name         : chr "Matt"
    ##   .. .. ..$ discriminator: chr "9003"
    ##   .. .. ..$ nickname     : chr "Matt"
    ##   .. .. ..$ isBot        : logi FALSE
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  1 obs. of  5 variables:
    ##   .. .. ..$ id           : chr "138749853256646658"
    ##   .. .. ..$ name         : chr "Cloth"
    ##   .. .. ..$ discriminator: chr "6913"
    ##   .. .. ..$ nickname     : chr "Cloth"
    ##   .. .. ..$ isBot        : logi FALSE
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  1 obs. of  5 variables:
    ##   .. .. ..$ id           : chr "133057778854920199"
    ##   .. .. ..$ name         : chr "Kyatastrophe"
    ##   .. .. ..$ discriminator: chr "6666"
    ##   .. .. ..$ nickname     : chr "Kyatastrophe"
    ##   .. .. ..$ isBot        : logi FALSE
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  1 obs. of  5 variables:
    ##   .. .. ..$ id           : chr "205771140235264001"
    ##   .. .. ..$ name         : chr "Matt"
    ##   .. .. ..$ discriminator: chr "9003"
    ##   .. .. ..$ nickname     : chr "Matt"
    ##   .. .. ..$ isBot        : logi FALSE
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  1 obs. of  5 variables:
    ##   .. .. ..$ id           : chr "158370770068701184"
    ##   .. .. ..$ name         : chr "Kintark"
    ##   .. .. ..$ discriminator: chr "0588"
    ##   .. .. ..$ nickname     : chr "Kintark"
    ##   .. .. ..$ isBot        : logi FALSE
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  1 obs. of  5 variables:
    ##   .. .. ..$ id           : chr "167756751767076864"
    ##   .. .. ..$ name         : chr "TheyCallMeOtter"
    ##   .. .. ..$ discriminator: chr "0538"
    ##   .. .. ..$ nickname     : chr "TheyCallMeOtter"
    ##   .. .. ..$ isBot        : logi FALSE
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  1 obs. of  5 variables:
    ##   .. .. ..$ id           : chr "133057778854920199"
    ##   .. .. ..$ name         : chr "Kyatastrophe"
    ##   .. .. ..$ discriminator: chr "6666"
    ##   .. .. ..$ nickname     : chr "Kyatastrophe"
    ##   .. .. ..$ isBot        : logi FALSE
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  1 obs. of  5 variables:
    ##   .. .. ..$ id           : chr "167756751767076864"
    ##   .. .. ..$ name         : chr "TheyCallMeOtter"
    ##   .. .. ..$ discriminator: chr "0538"
    ##   .. .. ..$ nickname     : chr "TheyCallMeOtter"
    ##   .. .. ..$ isBot        : logi FALSE
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. ..$ :'data.frame':  0 obs. of  0 variables
    ##   .. .. [list output truncated]
    ##   ..$ reference         :'data.frame':   707254 obs. of  3 variables:
    ##   .. ..$ messageId: chr [1:707254] NA NA NA NA ...
    ##   .. ..$ channelId: chr [1:707254] NA NA NA NA ...
    ##   .. ..$ guildId  : chr [1:707254] NA NA NA NA ...
    ##  $ messageCount: int 707254

Getting a list of lists, we find that we are only interested in one
section of the data - the `messages` list, and its content. Let’s
extract that and turn it into a dataframe

``` r
discord_messages_only <- tibble(initial_discord_dump$messages)
```

Now, we have to filter further, removing unnecessary columns like
attachments, isPinned, etc. Also, we want to unnest the author
dataframe, and remove columns that are unnecessary for analysis, namely
the `color`, `avatarUrl`, `nickname`, and `isBot` columns.

``` r
time_and_content_only <- discord_messages_only %>% 
  select(timestamp, content)

wow_help_authors_only <- discord_messages_only %>% 
  select(author)

wow_help_authors_only_df <- do.call(data.frame, wow_help_authors_only) %>% 
  select(-author.color, -author.avatarUrl, -author.nickname, -author.isBot)

wow_content_filtered <- bind_cols(time_and_content_only, wow_help_authors_only_df)

str(wow_content_filtered)
```

    ## tibble [707,254 x 5] (S3: tbl_df/tbl/data.frame)
    ##  $ timestamp           : chr [1:707254] "2016-07-21T16:09:25.875+00:00" "2016-07-21T18:25:32.99+00:00" "2016-07-21T18:26:32.652+00:00" "2016-07-21T19:01:35.011+00:00" ...
    ##  $ content             : chr [1:707254] "Welcome to the Q&A channel. This is for simple Questions to be answered by the community away from the more inv"| __truncated__ "It's already out, enjoy! <U+0001F603>" "why is mw monk so bad now?" "MW monk being bad is kinda subjective honestly. Asking around in the Monk discord - they don't seem to think it"| __truncated__ ...
    ##  $ author.id           : chr [1:707254] "54660964481499136" "106429844627169280" "95323568774053888" "54396900614340608" ...
    ##  $ author.name         : chr [1:707254] "Coan" "Carl" "Noshei" "Shue" ...
    ##  $ author.discriminator: chr [1:707254] "0001" "0001" "0001" "3697" ...

Viola! we now have a great looking data table for text analysis.
