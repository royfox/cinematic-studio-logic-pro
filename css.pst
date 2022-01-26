var IncludedLibraries = ["Cinematic Studio Strings", "Cinematic Studio Brass", "Cinematic Studio Woodwinds"]

var Libraries = {
    "Cinematic Studio Strings": {
        'Articulations': { // Articulation Id: CC58 Keyswitch CC, Delay, Trill Offset
            2: [10, 150], // Legato
            12: [27, 60], // Sforzando
            13: [24, 60], // Staccato
            14: [18, 60], // Staccatissimo
            15: [14, 60], // Spiccato
            16: [33, 60], // Pizzicato
            17: [37, 60], // Bartok Pizzicato
            18: [44, 60], // Col Legno
            19: [53, 10], // Harmonics
            20: [57, 10], // Tremelo
            21: [63, 10], // Measured Tremolo
            22: [48, 10, 2], // Whole Step Trills
            23: [48, 10, 1], // Half Step Trills
            4: [67, 50], // Marcato Legato
            26: [73, 50], // Marcato Legato with Overlay
        },
        'Legatos': {
            'Velocities': [64, 100, 128], // Velocity crossovers between legato styles
            'Delays': [330, 250, 100], // Delays of legato styles
        },
        'LegatoArticulationId': 2,
        'MarcatoArticulationIds': [4, 26],
        'LegatoNoteOffDelay': 50,
        'LegatoStartDelay': 30,
        'MarcatoStartDelay': 50
    },
    "Cinematic Studio Woodwinds": {
        'Articulations': { // Articulation Id: CC58 Keyswitch CC, Delay, Trill Offset
            2: [10, 120], // Legato
            4: [13, 60], // Repetitions
            6: [22, 60], // Staccato
            5: [17, 60], // Staccatissimo
            7: [27, 60], // Sforzando
            9: [63, 60], // Measured Repetitions,
            14: [53, 60], // Flutters,
            8: [48, 10, 2], // Whole Step Trills
            15: [48, 10, 1], // Half Step Trills
            10: [67, 50], // Marcato Legato
            12: [73, 50], // Marcato Legato with Overlay
        },
        'Legatos': {
            'Velocities': [64, 100, 128], // Velocity crossovers between legato styles
            'Delays': [220, 130, 90], // Delays of legato styles
        },
        'LegatoArticulationId': 2,
        'MarcatoArticulationIds': [10, 12],
        'LegatoNoteOffDelay': 50,
        'LegatoStartDelay': 30,
        'MarcatoStartDelay': 50
    },
    "Cinematic Studio Brass": {
        'Articulations': { // Articulation Id: CC58 Keyswitch CC, Delay, Trill Offset
            2: [10, 120], // Legato
            4: [13, 60], // Repetitions
            6: [22, 60], // Staccato
            5: [17, 60], // Staccatissimo
            7: [27, 60], // Sforzando
            17: [36, 60], // Muted Shorts
            18: [57, 60], // Muted Longs
            9: [63, 60], // Double Tongue,
            16: [43, 60], // Rips
            14: [53, 60], // Flutter
            8: [48, 10, 2], // Whole Step Trills
            15: [48, 10, 1], // Half Step Trills
            10: [67, 50], // Marcato Legato
            12: [73, 50], // Marcato Legato with Overlay
        },
        'Legatos': {
            'Velocities': [64, 128], // Velocity crossovers between legato styles
            'Delays': [210, 100, 90], // Delays of legato styles
        },
        'LegatoArticulationId': 2,
        'MarcatoArticulationIds': [10, 12],
        'LegatoNoteOffDelay': 50,
        'LegatoStartDelay': 30,
        'MarcatoStartDelay': 50
    }
}

var TrackDelay = 500
var KeyswitchChannelCC = 58

var PluginParameters = [{
    name:"Library", 
    type:"menu", 
    valueStrings: IncludedLibraries,
    defaultValue: 0
}];

var activeNotes = 0

function HandleMIDI(event) {
    // tracking number of notes, to know whether to use legato transitions
    if (event instanceof NoteOn) {
        activeNotes++;
    }
    if (event instanceof NoteOff) {
        activeNotes--;
    }
    // AllSoundOff event, reset legato counter
    if(event instanceof ControlChange && event['channel'] == 120) {
        activeNotes = 0;
    }
    if (event['isRealtime'] == true) { // If playing from the keyboard, do nothing
        event.send()
    } else {
        var library = Libraries[IncludedLibraries[GetParameter("Library")]]
        if (event['articulationID'] in library['Articulations']) {
            var keyswitchValue = library['Articulations'][event['articulationID']][0]
            var delay = library['Articulations'][event['articulationID']][1]
    
            // Special handling for trills, which need to have an additional note created to trigger the trill
            if(library['Articulations'][event['articulationID']].length == 3) {
                if (event instanceof NoteOn) {
                    sendTrillOn(event, delay, keyswitchValue, library['Articulations'][event['articulationID']][2])
                } else if (event instanceof NoteOff) {
                    sendTrillOff(event, delay, keyswitchValue, library['Articulations'][event['articulationID']][2])
                } else {
                    sendEvent(event, 0, keyswitchValue)
                }
            } else {
                // special handling for legatos, where the velocity is important 
                if (event['articulationID'] == library['LegatoArticulationId']) {   
                    if (event instanceof NoteOn) {
                        if (activeNotes == 1) {
                            delay = library['LegatoStartDelay'];
                        } else {
                            for (let i = 0; i < library['Legatos']['Velocities'].length; i++) { 
                                if(event['velocity'] <= library['Legatos']['Velocities'][i]) {
                                    delay = library['Legatos']['Delays'][i]
                                    break
                                }     
                            }
                        }
                    } else if (event instanceof NoteOff) {
                        delay = library['LegatoNoteOffDelay']
                    }
                 // Marcato handling - set shorter delay if not part of legato phrase  
                } else if (library['MarcatoArticulationIds'].includes(event['articulationID'])) {
                    if (event instanceof NoteOn) {
                        if (activeNotes == 1) {
                            delay = library['MarcatoStartDelay'];
                        } 
                    }
                }
    
                if (event instanceof NoteOn || event instanceof NoteOff) {
                    sendEvent(event, delay, keyswitchValue)
                } else {
                    sendEvent(event, 0, keyswitchValue)
                }
            } 
        } else {
            sendEvent(event, 0, 0)
        }
    }
}

function sendKeyswitchCC(event, delayOffset, keyswitchCCValue) {
    var controlChange = new ControlChange;
    if (keyswitchCCValue > 0) {
        controlChange['number'] = KeyswitchChannelCC;
        controlChange['value'] = keyswitchCCValue;
        controlChange['channel'] = event['channel'];
        controlChange['sendAfterMilliseconds'](delayOffset)
    }
}

function sendEvent(event, delay, keyswitchCCValue) {
    var delayOffset = TrackDelay - delay;
    sendKeyswitchCC(event, delayOffset - 1, keyswitchCCValue)
    event['sendAfterMilliseconds'](delayOffset);
}

function sendTrillOn(event, delay, keyswitchCCValue, trillNoteOffset) {
    var newNoteOn = new NoteOn;
    var trillNoteOn = new NoteOn;
    var delayOffset = TrackDelay - delay;
    sendKeyswitchCC(event, delayOffset, keyswitchCCValue)
    newNoteOn['channel'] = event['channel'];
    newNoteOn['velocity'] = event['velocity'];
    trillNoteOn['channel'] = event['channel'];
    trillNoteOn['velocity'] = event['velocity'];
    newNoteOn['pitch'] = event['pitch'];
    trillNoteOn['pitch'] = event['pitch'] + trillNoteOffset;
    trillNoteOn['sendAfterMilliseconds'](delayOffset);
    newNoteOn['sendAfterMilliseconds'](delayOffset + 1);
}

function sendTrillOff(event, delay, keyswitchCCValue, value) {
    var newNoteOff = new NoteOff;
    var trillNoteOff = new NoteOff;
    var delayOffset = TrackDelay - delay;
    sendKeyswitchCC(event, delayOffset, keyswitchCCValue)
    newNoteOff['channel'] = event['channel'];
    newNoteOff['velocity'] = event['velocity'];
    trillNoteOff['channel'] = event['channel'];
    trillNoteOff['velocity'] = event['velocity'];
    newNoteOff['pitch'] = event['pitch'];
    trillNoteOff['pitch'] = event['pitch'] + value;
    newNoteOff['sendAfterMilliseconds'](delayOffset);
    trillNoteOff['sendAfterMilliseconds'](delayOffset);
}