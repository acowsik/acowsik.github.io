---
layout: post
title: "The EigenWeapons of Zelda: Tears of the Kingdom"
author: "Rohan Rao"
categories: Video Game
image: totk_thumbnail.png
---

## The ROI of Defeating Monsters and Eigenweapons in Zelda: Tears of the Kingdom

Breath of the Wild (Tears of the Kingdom) is the critically acclaimed sequel to Breath of the Wild. The combat system of the original Breath of the Wild game was innovative for many reasons, but my personal favorite was that weapons break after you use them too much. This made the game fun as weapon management and finding new stronger weapons to replace existing weapons felt like a core part of the game.

Tears of the Kingdom added an interesting twist to this mechanic in which weapons are formed from items that can be  fused together (fuse a stick and a monster horn and you get a strong spear).

In tears of the kingdom there are two items that we care about for this post. Weapons and Materials.

Weapons in the game are items that can defeat monsters. Weapons deal damage every time you deal a hit and weapons can deal some number of hits until the weapon breaks. When we say damage we will generally refer to the damage per hit. The number of hits before breaking is called durability

So weapons can deal a ‘total damage’ of 

$$damage \cdot durability$$

Some examples of weapons in the game are tree branches, wooden sticks, and swords

When you defeat a monster they drop items (called Materials) that can be fused to your weapons to increase the attack and durability. These materials stay fused to your weapon throughout the lifetime of the weapon unless the material is fragile in which case it breaks after one hit.

So a fused weapon can deal (assuming a non fragile material) a total damage of

$$(weapon\_damage + material\_damage\_boost) \cdot (weapon\_durability + material\_durability\_boost)$$

Some examples of materials are monster claws. Horns, eyes, and teeth.

One way to think about your weapon progression is that you use up your fused weapon to defeat a monster that drops parts to build a new stronger fused weapon.

In this simplified model of weapon growth, I’m interested in what monsters provide good ROI to defeat and in the long run assuming some model of optimally “trading up” your weapon inventory what the steady states look like here.

### Getting Materials

<img src="assets/img/materials.png" width="300" align="center">

You get materials by defeating monsters. Monsters have some $$HP$$ and you hit them with your weapon until your weapon deals enough damage to deplete the HP to 0. Weapons deal some damage per hit (attack), and last some number of hits (durability).

When you fight a monster, and if $$attack \cdot durability > HP$$ you defeat the monster and the monster will drop some materials. If you end up using your weapon on an enemy of comparable strength then effectively you replace the material in your weapon with the new materials. Through this process we can think of fighting a monster as trading a material for new (hopefully better materials).

for example when fighting and defeating this guy (a Moblin) they drop a horn (and more)

<img src="assets/img/moblin loot drop.png" width="600" align="center">

just for another example, when defeating a bunch of Blue Bokoblins and their leader (a Boss Blue Bokoblin)

<img src="assets/img/blue_boko_montage.png" width="600" align="center">


We filter out materials with additional fuse damage $$\leq 1$$ just to focus on materials intended for damage combat

### Choices of Base Weapon

<img src="assets/img/base_weapons.png" width="300" align="center">

As mentioned earlier materials alone can't defeat monsters in our model - they need to be fused with some weapon to defeat monsters.

Our model describes the way we turn materials into more materials but but we need a steady source of available weapons that don't cost much to acquire to use as a weapon input to this model.

If we pick too fancy of a base weapon it becomes unrealistic to assume that we always have that weapon fusable in our inventory at every timestep.

I decided on 2 weapons

|weapon| base attack| base durability| fuse durability boost|
|tree branch| 2| 4| 10|
|wooden stick|4|12|25|

To give a range of available weapons to fuse with.

### What's the attack ROI for defeating monsters


To start with we can measure the expected attack in the loot that a monster drops against how much attack you use up defeating the monster. More precisely - if you fuse all the materials a monster drops to a common available weapon base, how does the expected damage you have compare to what you spent

Here's a plot (assuming we fuse to wooden stick):


    
![png](assets/img/Totk%20Monster%20Drops_2_0.png)
    



    
![png](assets/img/Totk%20Monster%20Drops_2_1.png)
    



    
![png](assets/img/Totk%20Monster%20Drops_2_2.png)
    


Generally looks like you get better damage ROI with weaker monsters - which makes sense since at the beginning of the game you have very little and you defeat weaker monsters to build up your inventory. The monsters that drop more premium items have worse ROI in part because cost a lot of resources to defeat. This also seems reasonable as you wouldn't want to naturally have players' inventories spiral out of control until the game becomes too easy.

Maybe then the equillibrium state of a players' inventory is somewhere between these things.

### Weapon Inventory Dynamics

Lets say you have some set of materials which we will represent as a vector $$x$$ where $$x_i$$ is the quantity of material $$i$$ you have.

with material $$i$$ lets say you fuse it with some weapon and defeat the 'best' monster you can with it. Lets make another matrix $$M$$ where $$M_{ij} = 1$$ if you chose to defeat monster $$i$$ with weapon $$j$$.

now $$k = Mx$$ gives us a vector where $$k_i$$ indicates how many of monster $$i$$ you defeat with your current inventory.

Now we construct another matrix $$D$$ where $$D_{ij}$$ is the expected number of item $$i$$ that monster $$j$$ drops.

Thus

$$x' = T \cdot x = D \cdot M \cdot x$$ is our new expected inventory after we use up our current inventory. This relies on the assumption that your used up weapon is functinally discarded after the battle. We'll refer to $$T$$ as our transition matrix.

This assumption is more reasonable if you are trying to defeat a monster whose HP uses up a lot of the damage of the weapon. 

There are a few different ways to choose $$M$$ and $$D$$ which we will discuss later

### A Linear Dynamical System for Weapons

once we have a model for how our inventory evolves after one cycle

$$x_{t+1} = T \cdot x_{t}$$

and in general

$$x_{t+n} = T^n \cdot x_{t}$$

A general way to analyze such systems is to look at the eigenvalues and eigenvectors of $$T$$.

If we have eigenvalues $$\lambda_i$$ and eigenvectors $$v_i$$

$$T^n \cdot x = T^n \cdot \sum_{i} c_i v_i = \sum_{i} c_i\lambda_i ^n v_i$$

so for $$\lambda_i$$ if $$\|\lambda_i\| > 1$$ that eigenvector component will 'grow' over time, if $$\|\lambda_i\| = 1$$ it will remain over time  and otherwise in the long run it will disappear. This can give us a sense for which materials will exist in our 'equillibrium' inventory.

### Choices of our Drops Matrix

Here there are two possible ways to model drops

#### Best Weapon Distribution
Lets pretend we only carry around one weapon and when we beat a monster we replace our weapon. It makes sense then that for monster index $$j$$

$$D_{ij} = \mathbb{P}(material\_i\_is\_the\_best\_dropped\_material)$$

where best is in attack terms

#### Full Weapon Distribution

If we instead pick up everything that is dropped then for monster index $$j$$

$$D_{ij} = \mathbb{E}[number\_of\_material\_i\_dropped]$$


### Greedy Single Monster Matrix

Based on whatever drop scheme $$D$$ we choose we'll pick the monster with the best loot that the fused weapon can defeat (so either based on the expected damage of the best material or based on the best expected damage of the total drop of material).

$$M_{ij} = 1$$ if Monster $$i$$ is a monster that can be defeated with weapon $$j$$ and if $$a$$ is a column vector where $$a_k$$ is the additional fuse damage that material $$k$$ does then monster $$i$$ has the highest $$a^T \cdot d_i$$ where $$d_i$$ is the $$i$$th column of $$D$$.

More concisely the $$j$$'th column of $$M_{ij}$$ (let call this $$m$$) is the solution to

$$\max a^T \cdot D \cdot m$$

under the constraints

$$m_i \geq 0$$

$$\sum_i m_i \leq 1$$

and $$m_{j,i} = 0$$ if monster $$j$$ cannot be defeated with weapon $$i$$

This is a linear program and so the optimum is on one of the vertices of the feasible space (the vertices are just the places where we pick exactly 1 monster to fight). So once we pick the best monster we know that this is optimal under our selection criteria.


Our per column result also works for the whole matrix

### Multiple Monster Matrix

Ok but what if we have leftover hits on our weapon. Surely we should model how we can get value out of our extra hits

We can come up with a monster matrix like 

$$\max a^T \cdot D \cdot m$$

under the constraints

$$m_i \geq 0$$

$$\sum_i m_i \cdot ceil(\frac{hp_i}{weapon\_damage}) \leq durability$$

and $$m_{j,i} = 0$$ if monster $$j$$ cannot be defeated with weapon $$i$$

AND $$m_{ij} \in \mathbb{Z}$$

Under this model though the optimal monster to defeat likely just ends up defeating as many of the best ROI monster as possible. This intuitively though doesn't capture the play style and game objectives of most people.

Instead of  modeling allowing using the weapon to defeat an arbitrary amount of monsters we'll add a linear penalty for the 'density' of the good items we get.

It's not obvious that a linear penalty is the right choice here but it lets us keep the optimization problem structure. Additionally one interpretation here is that if we have our damage distributed across 2 weapons, this means we have to deal twice as many hits to deal the same amount of damage. If we take a constant amount of damage per hit then we lose twice as much health. So if we are comparing two scenarios.

(1) receive 2 weapons which do 10 damage each (and take $$2x$$ damage)

(2) receive 1 weapon which does 20 damage (and take $$x$$ damage)

In scenario (1) I'd need to use $$2$$ times as many healing items as in scenario (2), so we can pretend that $$\alpha$$ is the conversion factor between healing hits and cost space. So (1) costs $$2\alpha$$ and (2) costs $$\alpha$$.

So lets adjust $$a$$ our cost function to compute the damage of each dropped item as $$\max(damage - alpha, 0)$$. I considered making $$\alpha$$ a function of the attack a monster does. But I chose not to because in practice the health/healing system is complicated so not clear if thats better.

This can also be interpreted as filtering out drops that we don't deem good enough (where $$\alpha$$ is our threshold).

We can compute $$M$$ using an off the shelf integer linear programming solver (its not too big of a problem).

Anyways - now that we have $$M$$ defined as a fuction of which $$D$$ we choose, we can analyze our many transition matrices

# Results

<img src="assets/img/monster_examples.png" width="600" align="center">

Now we have a lot of possible transition matrices $$T$$ to look at. For each possibility we'll look at the non-zero eigenvalues and corresponding eigenvectors of $$D \cdot M$$ for all 3 base weapons.

To keep the results concise We'll plot the eigenvalue magnitudes and list the monsters in the eigenvectors corresponding to the eigenvalues with magnitude $$\geq 1$$ (and list the eigenvectors below)

## Single Monster Matrix M

### Best Drop D

(1) Tree Branch Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_4_0.png)
    


    Eigenvalue:  1.00
    1.00 of Black Lizalfos Horn
    ---------------


(2) Wooden Stick Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_6_0.png)
    


    Eigenvalue:  1.00
    1.00 of Silver Boss Bokoblin Horn
    ---------------

### All Drops D

(1) Tree Branch Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_10_0.png)
    


    Eigenvalue:  3.00
    1.00 of Moblin Fang
    ---------------
    Eigenvalue:  1.25
    -0.06 of Fire-Breath Lizalfos Tail
    0.84 of Moblin Fang
    -0.49 of Lizalfos Talon
    -0.24 of Fire-Breath Lizalfos Horn
    ---------------
    Eigenvalue:  1.25
    0.82 of Moblin Fang
    -0.30 of Black Lizalfos Horn
    -0.48 of Lizalfos Talon
    -0.07 of Black Lizalfos Tail
    ---------------


(2) Wooden Stick Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_12_0.png)
    


    Eigenvalue:  4.17
    0.80 of Boss Bokoblin Fang
    0.31 of Black Lizalfos Horn
    0.50 of Lizalfos Talon
    0.08 of Black Lizalfos Tail
    0.12 of Black Boss Bokoblin Horn
    ---------------
    Eigenvalue:  -1.87
    -0.04 of Blue Boss Bokoblin Horn
    0.03 of Frox Fingernail
    0.04 of Frox Fang
    -0.87 of Boss Bokoblin Fang
    0.25 of Black Lizalfos Horn
    0.40 of Lizalfos Talon
    0.06 of Black Lizalfos Tail
    -0.11 of Black Boss Bokoblin Horn
    ---------------
    Eigenvalue:  1.87
    0.49 of Blue Boss Bokoblin Horn
    0.39 of Frox Fingernail
    0.52 of Frox Fang
    -0.07 of Boss Bokoblin Fang
    -0.24 of Black Lizalfos Horn
    -0.39 of Lizalfos Talon
    -0.06 of Black Lizalfos Tail
    -0.35 of Black Boss Bokoblin Horn
    ---------------
    Eigenvalue:  -1.57
    -0.85 of Boss Bokoblin Fang
    0.27 of Black Lizalfos Horn
    0.43 of Lizalfos Talon
    0.07 of Black Lizalfos Tail
    -0.13 of Black Boss Bokoblin Horn
    ---------------
    Eigenvalue:  -1.00
    -0.17 of Blue Boss Bokoblin Horn
    0.26 of Frox Fingernail
    0.34 of Frox Fang
    0.61 of Boss Bokoblin Fang
    -0.23 of Black Lizalfos Horn
    -0.38 of Lizalfos Talon
    0.06 of Silver Boss Bokoblin Horn
    -0.06 of Black Lizalfos Tail
    -0.06 of Molduga Jaw
    0.15 of Black Boss Bokoblin Horn
    -0.43 of Molduga Fin
    ---------------
    Eigenvalue:  1.00
    0.27 of Blue Boss Bokoblin Horn
    0.40 of Frox Fingernail
    0.54 of Frox Fang
    -0.09 of Silver Boss Bokoblin Horn
    -0.09 of Molduga Jaw
    -0.08 of Black Boss Bokoblin Horn
    -0.67 of Molduga Fin
    ---------------

## Multiple Monster Matrix M

### $$\alpha = 0$$

### Best Drop D

(1) Tree Branch Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_16_0.png)
    


    Eigenvalue:  14.00
    1.00 of Fire Keese Eyeball
    ---------------


(2) Wooden Stick Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_18_0.png)
    


    Eigenvalue:  37.00
    1.00 of Fire Keese Eyeball
    ---------------

### All Drops D

(1) Tree Branch Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_22_0.png)
    


    Eigenvalue:  42.00
    1.00 of Moblin Fang
    ---------------


(2) Wooden Stick Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_24_0.png)
    


    Eigenvalue:  111.00
    1.00 of Moblin Fang
    ---------------


### $$\alpha = 10$$

### Best Drop D

(1) Tree Branch Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_28_0.png)
    


    Eigenvalue:  2.00
    1.00 of Blue Lizalfos Horn
    ---------------
    Eigenvalue:  1.00
    -0.07 of Blue Lizalfos Horn
    0.07 of Gibdo Wing
    0.51 of Black Lizalfos Horn
    0.85 of Gibdo Bone
    ---------------
    Eigenvalue:  1.00
    -0.71 of Blue Lizalfos Horn
    0.71 of Soldier Construct III Horn
    ---------------
    Eigenvalue:  0.40
    -0.13 of Blue Lizalfos Horn
    0.21 of Gibdo Wing
    0.97 of Gibdo Bone
    ---------------


(2) Wooden Stick Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_30_0.png)
    


    Eigenvalue:  4.37
    0.76 of Blue Lizalfos Horn
    0.64 of Black Lizalfos Horn
    ---------------
    Eigenvalue:  -1.37
    0.86 of Blue Lizalfos Horn
    -0.51 of Black Lizalfos Horn
    ---------------
    Eigenvalue:  1.00
    -0.71 of Blue Lizalfos Horn
    0.71 of Soldier Construct III Horn
    ---------------
    Eigenvalue:  0.40
    -0.07 of Blue Lizalfos Horn
    0.21 of Gibdo Wing
    -0.04 of Black Lizalfos Horn
    0.97 of Gibdo Bone
    ---------------

### All Drops D

(1) Tree Branch Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_34_0.png)
    


    Eigenvalue:  2.56
    -0.74 of Blue Lizalfos Horn
    -0.04 of Gibdo Wing
    -0.59 of Lizalfos Talon
    -0.18 of Blue Lizalfos Tail
    -0.27 of Gibdo Bone
    ---------------
    Eigenvalue:  1.25
    0.23 of Blue Lizalfos Horn
    -0.28 of Gibdo Wing
    -0.32 of Black Lizalfos Horn
    -0.33 of Lizalfos Talon
    0.06 of Blue Lizalfos Tail
    -0.08 of Black Lizalfos Tail
    -0.80 of Gibdo Bone
    ---------------
    Eigenvalue:  0.17 + 0.31i
    0.29 + 0.04i of Blue Lizalfos Horn
    -0.69 of Gibdo Wing
    0.23 + 0.03i of Lizalfos Talon
    0.07 + 0.01i of Blue Lizalfos Tail
    -0.30 + -0.54i of Gibdo Bone
    ---------------
    Eigenvalue:  0.17 + -0.31i
    0.29 + -0.04i of Blue Lizalfos Horn
    -0.69 of Gibdo Wing
    0.23 + -0.03i of Lizalfos Talon
    0.07 + -0.01i of Blue Lizalfos Tail
    -0.30 + 0.54i of Gibdo Bone
    ---------------


(2) Wooden Stick Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_36_0.png)
    


    Eigenvalue:  7.03
    0.32 of Blue Lizalfos Horn
    0.05 of Gibdo Wing
    0.38 of Black Lizalfos Horn
    0.86 of Lizalfos Talon
    0.08 of Blue Lizalfos Tail
    0.09 of Black Lizalfos Tail
    0.05 of Gibdo Bone
    ---------------
    Eigenvalue:  -1.53
    -0.83 of Blue Lizalfos Horn
    -0.03 of Gibdo Wing
    0.49 of Black Lizalfos Horn
    0.12 of Lizalfos Talon
    -0.21 of Blue Lizalfos Tail
    0.12 of Black Lizalfos Tail
    -0.03 of Gibdo Bone
    ---------------
    Eigenvalue:  0.50
    -0.10 of Blue Lizalfos Horn
    0.46 of Gibdo Wing
    -0.11 of Black Lizalfos Horn
    -0.25 of Lizalfos Talon
    -0.02 of Blue Lizalfos Tail
    -0.03 of Black Lizalfos Tail
    0.12 of Soldier Construct III Horn
    0.06 of Zonai Charge
    0.83 of Gibdo Bone
    ---------------
    Eigenvalue:  0.25
    -0.05 of Blue Lizalfos Horn
    0.68 of Gibdo Wing
    -0.13 of Black Lizalfos Horn
    -0.25 of Lizalfos Talon
    -0.01 of Blue Lizalfos Tail
    -0.03 of Black Lizalfos Tail
    0.68 of Gibdo Bone
    ---------------



### $$\alpha = 20$$

### Best Drop D

(1) Tree Branch Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_40_0.png)
    


    Eigenvalue:  1.00
    0.08 of Gibdo Wing
    0.39 of Black Lizalfos Horn
    0.92 of Gibdo Bone
    ---------------
    Eigenvalue:  0.66
    0.13 of Gibdo Wing
    0.99 of Gibdo Bone
    ---------------
    Eigenvalue:  -0.26
    0.31 of Gibdo Wing
    -0.95 of Gibdo Bone
    ---------------


(2) Wooden Stick Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_42_0.png)
    


    Eigenvalue:  3.03
    -0.02 of Gibdo Wing
    -0.65 of Black Lizalfos Horn
    -0.76 of Gibdo Bone
    ---------------
    Eigenvalue:  0.53
    0.16 of Gibdo Wing
    -0.07 of Black Lizalfos Horn
    0.98 of Gibdo Bone
    ---------------
    Eigenvalue:  -0.16
    -0.47 of Gibdo Wing
    0.15 of Black Lizalfos Horn
    0.87 of Gibdo Bone
    ---------------

### All Drops D

(1) Tree Branch Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_46_0.png)
    


    Eigenvalue:  1.25
    -0.31 of Gibdo Wing
    -0.07 of Black Lizalfos Horn
    -0.11 of Lizalfos Talon
    -0.02 of Black Lizalfos Tail
    -0.94 of Gibdo Bone
    ---------------
    Eigenvalue:  1.12
    0.34 of Gibdo Wing
    0.94 of Gibdo Bone
    ---------------
    Eigenvalue:  -0.72
    -0.49 of Gibdo Wing
    0.87 of Gibdo Bone
    ---------------


(2) Wooden Stick Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_48_0.png)
    


    Eigenvalue:  5.53
    0.08 of Gibdo Wing
    0.48 of Black Lizalfos Horn
    0.77 of Lizalfos Talon
    0.12 of Black Lizalfos Tail
    0.40 of Gibdo Bone
    ---------------
    Eigenvalue:  0.86
    0.37 of Gibdo Wing
    -0.08 of Black Lizalfos Horn
    -0.13 of Lizalfos Talon
    -0.02 of Black Lizalfos Tail
    0.92 of Gibdo Bone
    ---------------
    Eigenvalue:  -0.63
    -0.60 of Gibdo Wing
    0.10 of Black Lizalfos Horn
    0.16 of Lizalfos Talon
    0.02 of Black Lizalfos Tail
    0.78 of Gibdo Bone
    ---------------

### $$\alpha = 30$$

### Best Drop D

(1) Tree Branch Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_52_0.png)
    


    Eigenvalue:  0.66
    -0.13 of Gibdo Wing
    -0.99 of Gibdo Bone
    ---------------
    Eigenvalue:  -0.26
    -0.31 of Gibdo Wing
    0.95 of Gibdo Bone
    ---------------


(2) Wooden Stick Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_54_0.png)
    


    Eigenvalue:  1.00
    0.09 of Gibdo Wing
    1.00 of Gibdo Bone
    ---------------
    Eigenvalue:  1.00
    -0.09 of Gibdo Wing
    0.00 of Silver Boss Bokoblin Horn
    -1.00 of Gibdo Bone
    ---------------
    Eigenvalue:  -0.60
    0.14 of Gibdo Wing
    -0.99 of Gibdo Bone
    ---------------

### All Drops D

(1) Tree Branch Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_58_0.png)
    


    Eigenvalue:  1.12
    -0.34 of Gibdo Wing
    -0.94 of Gibdo Bone
    ---------------
    Eigenvalue:  -0.72
    -0.49 of Gibdo Wing
    0.87 of Gibdo Bone
    ---------------


(2) Wooden Stick Base Weapon


    
![png](assets/img/Totk%20Monster%20Drops_60_0.png)
    


    Eigenvalue:  1.89
    0.21 of Gibdo Wing
    0.98 of Gibdo Bone
    ---------------
    Eigenvalue:  -1.49
    0.26 of Gibdo Wing
    -0.97 of Gibdo Bone
    ---------------
    Eigenvalue:  1.00
    -0.34 of Gibdo Wing
    0.29 of Boss Bokoblin Fang
    0.04 of Silver Boss Bokoblin Horn
    -0.89 of Gibdo Bone
    ---------------


# Conclusion

<img src="assets/img/ending_thumbnail.png" width="1000" align="center">


Under some different models we saw some similar monster items remain in the long run.

This doesn’t necessarily mean that all of these are optimal to try and obtain but it does explain some situations where you might find these persistent in your inventory

Some other observations:

In lots of situation Fire Keese Eyes are a good item

Monster parts with dominated ROIs won’t stay in your inventory in to long run under ‘optimal’ play

Despite being fragile, Gibdo bones seem quite good as you care more about damage density.

Some conventioonally strong monster parts (like Silver Lynel Horns) don't show up as much in our equillibria probably because with our base weapon its hard to defeat these monsters so we don't end up with these.

Lots of Lizalfos parts (of many colors) show up in the equillibria.

# Credits

- all raw data came from the Totk Data Spreadsheets
- https://docs.google.com/spreadsheets/d/1fBvQ17WHP3ASgtO8ode_rf1g4DfEHErMrHwwLppNTJM/edit?gid=1307828066#gid=1307828066
- https://docs.google.com/spreadsheets/d/1PhKeLdLqjLTLxRqsd3ZvzpUpU8RBSD6zXFgzmxIRSbM/edit?gid=21404910#gid=21404910

- Totk is a game published by and the property of Nintendo (https://zelda.nintendo.com/tears-of-the-kingdom/)

- screenshots were from my own gameplay
