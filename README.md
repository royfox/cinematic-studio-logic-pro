Articulation management for the Cinematic Studio Series libraries in Logic Pro X on Mac. This approach is based on an idea I saw on [Vi-control here](https://vi-control.net/community/threads/free-permanent-fix-for-css-legato.71972/), I just wanted to add a few more features to that concept. 

This is a little script that sets appropriate delays for the different articulations in the Cinematic Studio Series libraries. The idea is that you set a negative track delay on your track – the script then decides how much of that delay to remove for each note depending on the articulation chosen - you just need to set the articulation for each note in Logic, so that the script knows what delay to use. The articulation delay is only applied during playback, playing live from the keyboard should work as normal. 

### Installation instructions

- Copy the files in the articulations folder to ~/Music/Audio Music Apps/Articulation Settings
- Copy the css.pst file to ~/Music/Audio Music Apps/Plug-In Settings/Scripter

### Usage

- Set the corresponding articulation set for your track (strings, brass, winds) 
- Set the track delay to -500ms 
- Add Scripter as a midi effect to your track, and choose the Cinematic Studio Series script from the menu
- From the dropdown, select the appropriate library (strings, brass, winds) 
- Set articulations for all your notes, with any luck they should now all land on the beat 
