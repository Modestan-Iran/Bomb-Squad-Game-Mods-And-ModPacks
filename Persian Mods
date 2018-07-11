#Canvas

import bs
import random

def bsGetAPIVersion():
    return 4

def bsGetGames():
    return [Paint]

def bsGetLevels():
    return [bs.Level('Paint',
                     displayName='${GAME}',
                     gameType=Paint,
                     settings={},
                     previewTexName='courtyardPreview')]


class Dot(bs.Actor):
    def __init__(self, position=(0,0,0), color=(0,0,0), radius=(.5)):
        bs.Actor.__init__(self)
        self._r1 = radius
        if radius < 0: self._r1 = 0
        self.position = position
        self.color = color
        n1 = bs.newNode('locator',attrs={'shape':'circle','position':position,
                                         'color':self.color,'opacity':1,
                                         'drawBeauty':True,'additive':True})
        bs.animateArray(n1,'size',1,{0:[0.0],200:[self._r1*2.0]})
        self._node = [n1]

class Artist(bs.PlayerSpaz):
    def __init__(self, color=(1,1,1), highlight=(0.5,0.5,0.5), character="Spaz", sourcePlayer=None, startInvincible=True,
                 canAcceptPowerups=True, powerupsExpire=False, demoMode=False):
        self._player = sourcePlayer
        self.mode = 'نقاشی کشیدن'
        self.dotRadius = .5
        self.red = True
        self.blue = True
        self.green = True
        self.value = 1
        self.color = [1.0,0.0,0.0]
        bs.PlayerSpaz.__init__(self, color, highlight, character, sourcePlayer, powerupsExpire)
        
    def onBombPress(self):
        if self.mode == 'نقاشی کشیدن':
            self.dotRadius += .1
            self.setScoreText("اندازه: " + str(self.dotRadius), (1,1,1))
        elif self.mode == "رنگ":
            if self.color[0] >= 1:
                if self.color[2] == 0: self.color[1] += .1
                else: self.color[2] -= .1
            if self.color[1] >= 1:
                if self.color[0] == 0: self.color[2] += .1
                else: self.color[0] -= .1
            if self.color[2] >= 1:
                if self.color[1] == 0: self.color[0] += .1
                else: self.color[1] -= .1
                
            for i in range(len(self.color)):
                if self.color[i] < 0: self.color[i] = 0
                if self.color[i] > 1: self.color[i] = 1

            color = (self.color[0]*self.value, self.color[1]*self.value, self.color[2]*self.value) 
                    
            self.setScoreText("رنگ", color)
    def onPunchPress(self):
        if self.mode == 'نقاشی کشیدن':
            self.dotRadius -= .1
            if self.dotRadius < .05: self.dotRadius = 0
            self.setScoreText("اندازه: " + str(self.dotRadius), (1,1,1))
        elif self.mode == "رنگ":
            if self.color[0] >= 1:
                if self.color[1] == 0: self.color[2] += .1
                else: self.color[1] -= .1
            if self.color[1] >= 1:
                if self.color[2] == 0: self.color[0] += .1
                else: self.color[2] -= .1
            if self.color[2] >= 1:
                if self.color[0] == 0: self.color[1] += .1
                else: self.color[0] -= .1
            for i in range(len(self.color)):
                if self.color[i] < 0: self.color[i] = 0
                if self.color[i] > 1: self.color[i] = 1
            color = (self.color[0]*self.value, self.color[1]*self.value, self.color[2]*self.value) 
            self.setScoreText("رنگ", color)
    def onJumpPress(self):
        if self.mode == 'نقاشی کشیدن':
            color = (self.color[0]*self.value, self.color[1]*self.value, self.color[2]*self.value)
            pos = (self.node.positionCenter[0], self.node.positionCenter[1]-2, self.node.positionCenter[2])
            dot = Dot(position=pos, color = color, radius=self.dotRadius)
        elif self.mode == "رنگ":
            self.value += .1
            if self.value > 1 : self.value = 0
            self.setScoreText("پر رنگی: " + str(round(self.value,2)), (self.color[0]*self.value, self.color[1]*self.value, self.color[2]*self.value))
    def onPickUpPress(self):
        if self.mode == 'نقاشی کشیدن': self.mode = 'رنگ'
        elif self.mode == "رنگ": self.mode = "نقاشی کشیدن"
        self.setScoreText(self.mode + " انتخاب", (1,1,1))

class Paint(bs.CoopGameActivity):

    @classmethod
    def getName(cls):
        return 'Paint Persian'

    @classmethod
    def getScoreInfo(cls):
        return {'scoreType':'points'}

    @classmethod
    def getDescription(cls,sessionType):
        return 'نقاشی دلخواهت رو بکش'
    
    @classmethod
    def getSupportedMaps(cls,sessionType):
        return ['Doom Shroom']
        

    @classmethod
    def supportsSessionType(cls,sessionType):
        return True if issubclass(sessionType,bs.CoopSession) else False

    def __init__(self, settings):
        bs.CoopGameActivity.__init__(self, settings)
        self.info = bs.NodeActor(bs.newNode('text',
                                                   attrs={'vAttach': 'bottom',
                                                          'hAlign': 'center',
                                                          'vrDepth': 0,
                                                          'color': (0,1,0),
                                                          'shadow': 1.0,
                                                          'flatness': 1.0,
                                                          'position': (0,0),
                                                          'scale': 0.8,
                                                          'text': "Created by JAVAD on Github",
                                                          }))

    def onTransitionIn(self):
        bs.CoopGameActivity.onTransitionIn(self,music='ForwardMarch')

    def onBegin(self):
        bs.CoopGameActivity.onBegin(self)

    def spawnPlayerSpaz(self,player,position=(0,5,-3),angle=None):
        name = player.getName()
        color = player.color
        highlight = player.highlight
        spaz = Artist(color=color,
                             highlight=highlight,
                             character=player.character,
                             sourcePlayer=player)
        player.setActor(spaz)
        player.actor.connectControlsToPlayer()
        spaz.handleMessage(bs.StandMessage((0,3,0),90))
