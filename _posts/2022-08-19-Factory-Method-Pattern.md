---
layout: post
title:  "Factory Method Pattern"
summary: "Template in Factory Method Pattern"
author: Eussy
date: '2022-08-19 16:40:00 +0530'
category: 03_Design_Pattern
thumbnail: /assets/img/posts/code.jpg
keywords: devlopr jekyll, how to use devlopr, devlopr, how to use devlopr-jekyll, devlopr-jekyll tutorial,best jekyll themes
usemathjax: true
published : true
permalink: /blog/03_Design_Pattern/Factory-Method-Pattern/
---

first

```c++
#include <iostream>

class TGamePlayer {
public:
    virtual void Render() = 0;
};
class TApiGamePlayer : public TGamePlayer {
public:
    virtual void Render()
    {
        std::cout << "TApiGamePlayer" << std::endl;
    }
};
class TDxGamePlayer : public TGamePlayer {
public:
    virtual void Render()
    {
        std::cout << "TDxGamePlayer" << std::endl;
    }
};

class TGameMap {
public:
    virtual void Render() = 0;
};
class TApiGameMap : public TGameMap {
public:
    virtual void Render()
    {
        std::cout << "TApiGameMap" << std::endl;
    }
};
class TDxGameMap : public TGameMap {
public:
    virtual void Render()
    {
        std::cout << "TDxGameMap" << std::endl;
    }
};


class TGraphics{
public:
    virtual TGamePlayer* CreateGamePlayer() = 0;
    virtual TGameMap* CreateGameMap() = 0;
};
class TApiGraphics : public TGraphics 
{
public:
    virtual TGamePlayer* CreateGamePlayer()
    {
        return new TApiGamePlayer;
    }
    virtual TGameMap* CreateGameMap() 
    {
        return new TApiGameMap;
    }
};
class TDxGraphics : public TGraphics 
{
public:
    virtual TGamePlayer* CreateGamePlayer()
    {
        return new TDxGamePlayer;
    }
    virtual TGameMap* CreateGameMap()
    {
        return new TDxGameMap;
    }
};




int main()
{
    TGraphics* graphic = new TApiGraphics();
    
    TGamePlayer* gp = graphic->CreateGamePlayer();
    TGameMap* gm = graphic->CreateGameMap();

    gp->Render();
    gm->Render();

}
```