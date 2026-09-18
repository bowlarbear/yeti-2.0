In general, the core design of the main guide is relatively fixed. Tradeoffs and design direction have already been established. Our goal from this point forward is twofold.

1. Hone the readability of the main guide as a start-to-finish process, improve clarity or language in any areas that give users trouble. Fix any issues that might arise out of the bash syntax, and keep the guide and its API calls current with the latest software releases.

2. Add subguides. This is the area where there is the most room for community engagement. We believe the best approach is to keep the main guide tight and relatively short so that it is less psychologically intimidating. We love the idea of adding video guides, screenshots, and more thoroughly detailed explanations of some of the processes that are not explained start-to-finish in the guide. 

The general idea is that we hyperlink concepts that come up in the main guide and link to explainer documents that might walk the user through a process in greater detail, explain some of the high level concepts in a greater depth, provide screenshots to help users become familiar with the interfaces, and even video walkthroughs of part or all of the process.

We have one example of this already with the `verify_ubuntu.md` subguide. This is linked to from the main guide where appropriate, there are open issues for some other subguide ideas that would be nice to have, we are open to suggestions.

We are open to the idea of some subguides for optional changes to the setup, although this will require more extensive review because we are targeting a very opinionated key management system. We do not believe that choose-your-own-adventure style key management is the best practice when making recomendations. 

Perhaps one of the most important considerations still up for debate should be the upper bound $ value this system is appropriate for securing. We are confident that one could design a system to securely store $100M+, but that is not the design objective for yeti-2.0. Rather, the design objective is to provide reasonable and accessible security practices to capable but less technical users. 