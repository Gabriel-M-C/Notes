# Github AI access
- Give acces to github to an AI without control would be irresponsible.
- I have created a personal API key with **content** permisions for a only one repositorie.
- I have created an alias in my ./bashrc similar to this:
```
alias gh-notes='GH_TOKEN="github_pat_xxxxxxxxxxxxxxx..."'
```
- This way I can have some alias for each project I want to share with the AI.

So the Ai only needed to use this alias. 
The result was bad and the AI itself extract de Key and tried to use in a diferent way, bad instructions.
The key was only read mode so I created another key with write access and executed this myself to simplify my live (It is supossed AI simplify my live but ... hey ... simplicity is beautiful)

```
cd ~/Public/Notes
gh-notes auth setup-git --hostname github.com
git push origin main
```
