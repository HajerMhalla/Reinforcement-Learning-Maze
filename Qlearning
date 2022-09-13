extensions [Nw]

;; defining global variables
globals
[
  steps
  sum-rewards-per-episode
  acc-reward
  tiles
  new-links-red
  reward-per-episode
  episode
  average-reward Hlist
]

;; defining breeds
breed [nodes node]
breed [builders builder]
breed [maze-runners mr]

;; defining breeds variable
nodes-own [node-id maze-entrance maze-exit exit? corner? reward Qlist  ]
builders-own [stack]
maze-runners-own [prev-node current-node next-node next-path visited-nodes
                  visited-hubs I-found-exit? ]
patches-own [ preward pentrance qlist1 num-possible-actions possible-actions]

;; all functions defined here

;; setup button
to setup
  clear-all
  build-tiles
  init-nodes
  set episode 1
  build-maze
  set-entrance-exit

  setup-maze-runners



    ask nodes with [label = "exit"][set reward 100 set plabel reward]
    ask nodes with [maze-exit = False and exit? = True  ][set reward -20 set plabel reward set color blue]
    ask nodes with [label = "entrance"][set reward -20 set plabel reward]
    ask patches [set preward 0]
  ask nodes with [label = "entrance"][set color green]

  ask patches [set preward [reward] of nodes-here  ]
  ask patches with [pcolor = 82] [set preward -200]
  ask patches with [ pcolor = 9.91 ] [set Qlist1 [0 0 0 0 ]]
  ask patches [ set num-possible-actions  count patches at-points [[-2  0] [ 2  0 ] [0  -2] [0  2]] with [pcolor = 9.91]]
  ask patches [set possible-actions [-1 -1 -1 -1 ]]

  ask patches with [ pcolor = 9.91]  [if ( count patches at-points [[-2  0]]  with [pcolor = 9.91] = 1) [set possible-actions replace-item 2 possible-actions 270]]
  ask patches with [ pcolor = 9.91]  [if ( count patches at-points [[ 2  0]]  with [pcolor = 9.91] = 1) [set possible-actions replace-item 0 possible-actions 90]]
  ask patches with [ pcolor = 9.91]  [if ( count patches at-points [[ 0  2]]  with [pcolor = 9.91] = 1) [set possible-actions replace-item 3 possible-actions 0]]
  ask patches with [ pcolor = 9.91]  [if ( count patches at-points [[ 0 -2]]  with [pcolor = 9.91] = 1) [set possible-actions replace-item 1 possible-actions 180]]



  ask patches with[ pcolor = 9.91]  [ask patches at-points [[1 0]] [set pcolor  9.91]]
  ask patches with[ pcolor = 9.91]  [ask patches at-points [[-1 0]] [set pcolor  9.91]]
  ask patches with[ pcolor = 9.91]  [ask patches at-points [[0 -1]] [set pcolor  9.91]]
  ask patches with[ pcolor = 9.91]  [ask patches at-points [[0 1]] [set pcolor  9.91]]
    ask patches with[ pcolor = 9.91]  [ask patches at-points [[1 0]] [set pcolor  9.91]]
  ask patches with[ pcolor = 9.91]  [ask patches at-points [[-1 0]] [set pcolor  9.91]]
  ask patches with[ pcolor = 9.91]  [ask patches at-points [[0 -1]] [set pcolor  9.91]]
  ask patches with[ pcolor = 9.91]  [ask patches at-points [[0 1]] [set pcolor  9.91]]


;;0 up
;;90 ymin
;; 180down
;;270 ysar
 ;; (90 180 270 0)

   clear-all-plots

  reset-ticks
end

;; use in order to run simulation on the same maze several times
to reset-maze-runners
  reset-ticks
  set episode 1
  ask links [set color black set thickness 0]
  ask maze-runners [die]
  ask patches [set Qlist1 [0 0 0 0 ]]
  setup-maze-runners
 clear-all-plots
end


;; build orderd white tiles in the world
;; according to the spacing (their distance)
to build-tiles
  ask patches [set pcolor 82]
  set tiles patches with
  [ pxcor mod spacing = 0
    and pycor mod spacing = 0
    and abs pxcor +  spacing < max-pxcor
    and abs pycor +  spacing < max-pycor
    and abs pxcor -  spacing > min-pxcor
    and abs pycor - spacing > min-pycor
  ]
  ask tiles [ set pcolor white ]
  set new-links-red 0
end

;; Init nodes of given color,size and shape on each nodes
;; All boolear variables are set false
to init-nodes
  let index 1
  ask tiles
  [
    sprout-nodes 1
    [
       set color black
       set size 1
       set shape "circle"
       set node-id index
       set exit? false
       set maze-entrance false
       set maze-exit false
       set corner? false
     ]
     set index index + 1
  ]
  ask nodes [set reward -1 set plabel reward set Qlist [0 0 0 0] ]






end



;; Build maze
to build-maze

  create-builders  1
  [ ;; choose a random starting point
    let start one-of tiles
    set xcor [pxcor] of start
    set ycor [pycor] of start

    ;; set heading and color
    set heading 0
    set color blue
    ask patches in-radius 1 [ set pcolor [color] of myself ]
    set stack []
  ]
  ask builders
  [ ;; store starting point
    set stack fput ( list xcor ycor ) stack
    while [ length stack > 0 ]
    [ ;; in this while the maze building process
      let target 0
      let left-right 0
      let straight 0
      let running 0
      let paths find-open-paths
      ifelse any? paths
      [ ;; ifelse any? paths --> paths is not-empty
        set straight patch-ahead spacing
        set left-right paths with [ self != straight ]
        let node1 0
        if (any? nodes-on patch-here)
        [ ask one-of nodes-on patch-here [set node1 self] ]
        ifelse (any? left-right ) or not is-open straight
        [
          set target one-of left-right
          ;; record stack
          set stack fput ( list xcor ycor ) stack
          set heading towards target
          draw-move
        ]
        [
          set running true
          while [ running ]
          [
            set heading towards straight
            draw-move
            set straight patch-at ( dx * spacing) ( dy * spacing )
            set running ( random-float 1.0 >= 1 and is-open straight )
          ]
        ]
        if (any? nodes-on patch-here)
        [ask one-of nodes-on patch-here
          [create-link-with node1 [set color black]]]

       ]
      [ ;; ifelse any? paths --> path is empty
        ifelse length stack > 0
        [ ;; start the building process
          setxy (item 0 (item 0 stack)) (item 1 (item 0 stack))
          ;; removing first element from stack
          set stack but-first stack
         ]
         [ stop ]


    ]
 ]
    let i  0
    while[ i < 7 ]
    [let start one-of nodes
      set xcor [pxcor] of start
      set ycor [pycor] of start
      set heading 0

    ask patches in-radius 1 [ set pcolor [color] of myself ]
    let fin one-of nodes with [(pxcor = [pxcor ]of start and pycor =[pycor] of start + spacing  ) or (pxcor = [pxcor ]of start and pycor =[pycor] of start - spacing  ) or
   (pxcor = [pxcor ]of start + spacing  and pycor =[pycor] of start ) or (pxcor = [pxcor ]of start - spacing and pycor =[pycor] of start   )]
    ask  start [create-link-with fin [set color black]]
    let straight patches with [pxcor = [xcor] of fin  and pycor = [ycor] of fin  ]

    let running true
          while [running]
          [
            set heading towards fin
            draw-move2
            set straight patch-at ( dx * spacing) ( dy * spacing )
            set running ( random-float 1.0 >= 1 and is-open straight )
    ] set i i + 1]









    ;;close while
    die
  ];; close ask builders
end

;; draw move
to draw-move
  let start-spot patch-here


  ask start-spot [ ask patches in-radius 1  [ set pcolor 9.91 ] ]


  repeat spacing [ ask patches in-radius 1 [ set pcolor 9.91 ] jump 1  ]
 end
to draw-move2
  let start-spot patch-here
  ask start-spot [ ask patches in-radius 1 [ set pcolor 9.91 ] ]
  repeat spacing + 1  [ ask patches in-radius 1 [ set pcolor 9.91 ] jump 1 ]
 end

;;;;;;;;;;;;;;;;;;;;;;;;line to keep code in 80 columns;;;;;;;;;;;;;;;;;;;;;;;;

;; find maze entrance and exit
to set-entrance-exit

  let set-nodes-exit false
  let minx min [xcor] of nodes
  let miny min [ycor] of nodes
  let maxx max [xcor] of nodes
  let maxy max [ycor] of nodes
  let edge-nodes nodes with [
    pxcor = minx or pxcor = maxx or pycor = miny or pycor = maxy ]
  ask edge-nodes
  [
    ;set color black
    if (pxcor = minx and pycor = miny) [set corner? true]
    if (pxcor = minx and pycor = maxy) [set corner? true]
    if (pxcor = maxx and pycor = miny) [set corner? true]
    if (pxcor = maxx and pycor = maxy) [set corner? true]
  ]


  ask nodes
  [
    let exit-found? false
    ask patch-here
    [
       if (count neighbors with [pcolor = 82 ] = 5 ) [set exit-found? true]
       if (count neighbors with [pcolor = 82 ] = 2
        and count neighbors with [pcolor = 9.91] = 6 ) [
        set exit-found? true]
    ]
    if exit-found? = true  [set color black set size 2 set exit? true]
  ]
  let minx-exit min [xcor] of nodes with [exit? = true]
  let miny-exit min [ycor] of nodes with [exit? = true]
  let maxx-exit max [xcor] of nodes with [exit? = true]
  let maxy-exit max [ycor] of nodes with [exit? = true]
  ;; let's define two possible exit, one in the edge, the other even in the middle
  let edge-inout-nodes edge-nodes with [exit? = true]
  let inout-nodes nodes with [exit? = true]
  let possible-entrance one-of edge-inout-nodes

  if possible-entrance = nobody
  [ while [possible-entrance = nobody]
    [ set possible-entrance one-of inout-nodes]
  ]
  ask possible-entrance
  [ set maze-entrance true
    set label-color black
    set label "entrance"
    ask patch-here [set pentrance "entrance"]
    set color green
    set size 3

    (ifelse
    pxcor = minx-exit
    [
      if debug >= 1 [print "pxcor = minx-exit"]
      let possible-exit one-of edge-inout-nodes with [pxcor = maxx-exit]
      ifelse possible-exit != nobody
        [
          ask possible-exit
          [
            set maze-exit true set color red set size 3
            set label-color black set label "exit"
          ]
        ]
        [
          set possible-exit one-of inout-nodes with [label != "entrance"]
          ifelse possible-exit != nobody
          [
            ask possible-exit
            [
              set maze-exit true set color red set size 3
              set label-color black set label "exit"
            ]
          ]
          [
            print "Unable to find and entrance"
            print "Check spacing or other parameters"
          ]
        ]
      ]

    pxcor = maxx-exit
    [
        if debug >= 1 [print "pxcor = maxx-exit"]
        let possible-exit one-of edge-inout-nodes with [pxcor = minx-exit]
      ifelse possible-exit != nobody
        [ ask possible-exit
          [ set maze-exit true set color red set size 3
              set label-color black set label "exit"
          ]
        ]
        [ set possible-exit one-of inout-nodes with [label != "entrance"]
          ifelse possible-exit != nobody
          [
            ask possible-exit
            [
              set maze-exit true set color red set size 3
              set label-color black set label "exit"
            ]
          ]
          [
            print "Unable to find and entrance"
            print "Check spacing or other parameters"
          ]
        ]
      ]

    pycor = miny-exit
    [
        if debug >= 1 [print "pycor = miny-exit"]
        let possible-exit one-of edge-inout-nodes with [pycor = maxy-exit]
      ifelse possible-exit != nobody
        [
          ask possible-exit
          [
            set maze-exit true set color red set size 3
            set label-color black set label "exit"
          ]
        ]
        [ set possible-exit one-of inout-nodes with [label != "entrance"]
          ifelse possible-exit != nobody
          [
            ask possible-exit
            [
              set maze-exit true set color red set size 3
              set label-color black set label "exit"
            ]
          ]
          [
            print "Unable to find an entrance"
            print "Check spacing or other parameters"
          ]
        ]
      ]

    pycor = maxy-exit
    [
      if debug >= 1 [print "pycor = maxy-exit"]
      let possible-exit one-of edge-inout-nodes with [pycor = miny-exit]
      ifelse possible-exit != nobody
        [
          ask possible-exit
          [
            set maze-exit true set color red set size 3
            set label-color black set label "exit"
          ]
        ]
        [
          set possible-exit one-of inout-nodes with [label != "entrance"]
          ifelse possible-exit != nobody
          [
            ask possible-exit
            [
              set maze-exit true set color red set size 3
              set label-color black set label "exit"
            ]
          ]
          [ print "Unable to find and entrance"
            print "Check spacing or other parameters"
          ]
        ]
      ]
    )


  ]
end

;;;;;;;;;;;;;;;;;;;;;;;;line to keep code in 80 columns;;;;;;;;;;;;;;;;;;;;;;;;

;; setup maze runners
to setup-maze-runners
  ask one-of nodes with [label = "entrance"]
  [ let present-node self
    ask patch-here
    [ sprout-maze-runners 1
      [ set size 10
        set color yellow - 1
        set current-node present-node
        set visited-nodes []
        set visited-hubs []
        set I-found-exit? false
      ]
    ]
  ]
end

;;;;;;;;;;;;;;;;;;;;;;;;line to keep code in 80 columns;;;;;;;;;;;;;;;;;;;;;;;;

to find-exit

  ask maze-runners
  [
    set visited-nodes lput current-node visited-nodes
    ifelse [label] of current-node = "entrance"
    [ ;; current-node is entrance
      if debug >= 1 [print "current node is entrance"]
      set next-path one-of [my-links] of current-node
      ifelse [color] of next-path = black
      [ ;;next path black
        if debug >= 1 [print "next path is black"]
        ifelse current-node = [end1] of next-path
        [set next-node [end2] of next-path] [set next-node [end1] of next-path]
        color-link-green
        forward-maze-runner
      ]
      [ ;;next path NOT black
        print "next black not black at entrance not defined"
      ]
    ]
    [ ;; current-node is NOT entrance
;      if debug >= 1 [print "current node is NOT entrance"]
      ifelse [exit?] of current-node = true
      [ ;; current node is a blind spot, could be an exit
        if debug >= 1 [print "current node is a blind spot"]
        ifelse [maze-exit] of current-node = true
        [ ;; exit found
          set I-found-exit? true
          if [color] of next-path != green
          [color-best-path]
          if debug >= 1 [print "exit found"]
        ]
        [ ;;exit NOT found
          color-link-red
          go-back
        ]
      ]
      [ ;; current node is NOT a blind spot
;        if debug >= 1 [print "current node NOT is entrance"]
        ifelse [color] of link [who] of prev-node
                               [who] of current-node = green
        [ ;; previous path is green
          if debug >= 1 [print "previous path is green"]
          ifelse count [my-links] of current-node > 2
          [ ;; node is a hub
            if debug >= 1 [print "node is a hub"]
            found-new-hub
            set next-path search-link green
            ifelse next-path != nobody
            [ ;; next path is green
              if debug >= 1 [print "next path is green"]
              ifelse current-node = [end1] of next-path
              [set next-node [end2] of next-path]
              [set next-node [end1] of next-path]
              forward-maze-runner
;;;;;;;;;;;;;;;;;;;;;;;;line to keep code in 80 columns;;;;;;;;;;;;;;;;;;;;;;;;
            ]
            [discover-unknown-hub]
          ]
          [ ;; node is NOT a hub
            set next-path search-link black
              ifelse next-path != nobody
              [ ;; next path is black
              if debug >= 1 [print "next-path is black"]
                ifelse current-node = [end1] of next-path
                 [ set next-node [end2] of next-path ]
                 [ set next-node [end1] of next-path ]
                color-link-green
                forward-maze-runner
             ]
            [print "next-path not black after green not defined"]
          ]
        ]
        [ ;; previous path is NOT green
          ifelse [color] of link [who] of prev-node [who] of current-node = yellow
          [ ;; previous path is yellow
            if debug >= 1 [print "prev-path is yellow"]
            ifelse count [my-links] of current-node > 2
            [ ;; node is a hub
              if debug >= 1 [print "node is hub"]
              found-new-hub
              discover-unknown-hub
            ]
            [ ;; node is NOT a hub
              set next-path search-link black
              ifelse next-path != nobody
              [ ;; next path is black
              ifelse current-node = [end1] of next-path
                [set next-node [end2] of next-path]
                [set next-node [end1] of next-path]
              color-link-yellow
              forward-maze-runner
              ]
              [ ;;next path is NOT black
                print "previous yellow next not black not defined"
              ]
            ]
          ]
          [print "previous path is not green and yellow not defines"]
        ]
    ]
   ]
  ]
;  tick
  ifelse new-links-red != 0
  [tick-advance new-links-red set new-links-red 0]
  [ if not mr-found-exit? [tick] ]
  if debug >= 1 [print ticks]
  if mr-found-exit? [stop]

end

;;;;;;;;;;;;;;;;;;;;;;;;line to keep code in 80 columns;;;;;;;;;;;;;;;;;;;;;;;;

to forward-maze-runner
  if debug >= 1 [print "forward"]

  fd [link-length] of link [who] of current-node [who] of next-node
  set prev-node current-node
  set current-node next-node
end

to go-back
  if debug >= 1 [print "go-back"]
  set current-node last visited-nodes
  set visited-nodes remove current-node visited-nodes
  set visited-hubs remove current-node visited-hubs
  set prev-node last visited-nodes
  set xcor [xcor] of current-node
  set ycor [ycor] of current-node
  set next-path link [who] of prev-node [who] of current-node
  set heading report-mr-direction + 180
end

to color-link-green
  ask link first [who] of current-node first [who] of next-node
    [set color red set thickness 1]
end

to color-link-yellow
  ask link [who] of current-node [who] of next-node [set color yellow]
end


to color-link-red
  let last-node last visited-nodes
  let before-last-node item (length visited-nodes - 2) visited-nodes
  if last-node = last visited-hubs
  [ ;;this happens when mr is in a hub and all branch are red
    ;;in order to go back we need to remove the last visited-hubs
    set visited-hubs remove last visited-hubs visited-hubs
  ]
  if debug >= 2
  [
    print "color-link-red"
    print "last visited hub"
    print last visited-hubs
    print "last-node in visited-nodes"
    print last-node
    print "before-last-node in visited-nodes"
    print before-last-node
    print "link color red:"
  ]
  while [last-node != last visited-hubs]
  [
    ask link [who] of last-node [who] of before-last-node [set color red]
    if debug >= 2 [print link [who] of last-node [who] of before-last-node]
    set visited-nodes remove last-node visited-nodes
    set last-node last visited-nodes
    set before-last-node
      item (position last-node visited-nodes - 1) visited-nodes
    set new-links-red new-links-red + 1
  ]
end
to go-up

  ask maze-runners [set heading 90]
end
to color-best-path
  let last-node last visited-nodes
  let before-last-node item (length visited-nodes - 2) visited-nodes
  if debug >= 2
  [
    print "color-best-path"
    print "last visited hub"
    print last visited-hubs
    print "last-node in visited-nodes"
    print last-node
    print "before-last-node in visited-nodes"
    print before-last-node
    print "link color green:"
  ]
  while [last-node != first visited-hubs]
  [
    ask link [who] of last-node [who] of before-last-node
    [set color green set thickness 1]
    if debug >= 2 [print link [who] of last-node [who] of before-last-node]
    set visited-nodes remove last-node visited-nodes
    set last-node last visited-nodes
    set before-last-node
      item (position last-node visited-nodes - 1) visited-nodes
  ]
end

;;;;;;;;;;;;;;;;;;;;;;;;line to keep code in 80 columns;;;;;;;;;;;;;;;;;;;;;;;;

to discover-unknown-hub
  if debug >= 1 [print "discovery new hub"]
  set next-path search-link green
  ifelse next-path != nobody
  [ ;;next path is green
    if debug >= 1 [print "next-path is green"]
    forward-maze-runner
  ]
  [ ;; next path is NOT green
    set next-path search-link black
    ifelse next-path != nobody
    [ ;; next path is black
      if debug >= 1 [print "next path is black"]
      ifelse found-best-path?
      [ ;;one next-path is red and all others red
        if debug >= 1 [print "one next-path is black and all others red"]
        set visited-hubs remove last visited-hubs visited-hubs
        ifelse current-node = [end1] of next-path
        [set next-node [end2] of next-path][set next-node [end1] of next-path]
        color-link-green
        forward-maze-runner
      ]
      [ ;; more next-path black
        ifelse current-node = [end1] of next-path
        [set next-node [end2] of next-path][set next-node [end1] of next-path]
        color-link-yellow
        forward-maze-runner
      ]
    ]
    [ ;; there are not next path black
      set next-path search-link yellow
      ifelse next-path != nobody
      [ ;; next-path is yellow
        if debug >= 1 [print "next-path is yellow"]
        forward-maze-runner
      ]
      [ ;; there are not next path yellow
        set next-path search-link red
        ifelse next-path != nobody
        [ ;; next-path is red
          if debug >= 1 [print "there are only red path"]
          color-link-red
          go-back
        ]
        [ ;; next-path is NOT red]
          print "Error, this scenario should not happen"
        ]
      ]
    ]
  ]
end

to found-new-hub
  if debug >= 1 [print "found new hub"]
  set visited-hubs lput current-node visited-hubs
end

;; all to-report functions defined here

;; find open path
to-report find-open-paths
  let paths
  ( patches at-points
    (map [ [a b] -> ;;procedure anonyme
      ( list(a * spacing ) (b * spacing) ) ] [ 0 0 1 -1 ] [1 -1 0 0 ])
   ) with [ pcolor = white ]
  report paths
end

;; check if path is open
to-report is-open
  [ a-patch ]
   report ([pcolor] of a-patch = white)
end

to-report report-mr-direction
  let lh 45
  ifelse current-node = [end1] of next-path
  [set lh [link-heading] of next-path][set lh
    [link-heading] of next-path + 180]
  report lh

end

to-report search-link [link-color]
  let new-link nobody
  let temp-prev-node prev-node
  ask current-node
  [ set new-link one-of
    (my-links with [color = link-color and other-end != temp-prev-node])
  ]
  report new-link
end

to-report found-best-path?
  if debug >= 1 [print "search for best bath"]
  let temp-prev-node prev-node
  let count-prev-path-green 0
  let count-next-path-black 0
  let count-next-path-red 0
  let total-path 0
  ask current-node
  [
    set count-prev-path-green count my-links with [color = green]
    set count-next-path-black count my-links with [other-end != temp-prev-node and color = black]
    set count-next-path-red count my-links with [other-end != temp-prev-node and color = red]
    set total-path count my-links
  ]
  if debug >= 2
  [
    print "count-prev-path-green"
    show count-prev-path-green
    print "count-next-path-black"
    show count-next-path-black
    print "count-next-path-red"
    show count-next-path-red
    print "total links"
    show total-path
  ]
  ifelse (count-next-path-black = 1) and
         (total-path = count-prev-path-green +
                            count-next-path-black + count-next-path-red )
  [ report true ][report false]
end

to-report mr-found-exit?
  let a-mr-found-exit? false
  let mr-on-exit one-of maze-runners with [I-found-exit? = true]
  if mr-on-exit != nobody [set a-mr-found-exit? true]
  report a-mr-found-exit?
end


to q_epsilon_decay
  set-current-plot "steps-per-episode"

  set-plot-pen-color white
  set exploration 1
  set Hlist [90 180 270 0]
  ;; set episode 1
  plot-pen-down
  set steps 0
  let xx [pxcor] of patches with [pentrance = "entrance"]
  let yy  [pycor] of patches with [pentrance = "entrance"]
  ask maze-runners[while [episode <= num-episodes]
  [
     ask links  [set color black set thickness 0 ]
    ask maze-runners [setxy  item 0 xx item 0 yy ]
    set sum-rewards-per-episode 0
    set reward-per-episode []
    pen-down
      set steps 0
    ask maze-runners [ while[  [preward] of patch-here != [100] ] ;; when episode ends
    [
          set steps steps + 1
        set current-node nodes-on patch-here
        print heading
        let Qnew 1
        let Qmax 0
        let dirp 0
        let rand random-float 1
        ifelse rand <= exploration
        [;;epsilon  action selection ;; on peut ajouter une conditions bish meyekhtaresh el wall?
            let dir one-of Hlist  ;--pick random direction
            set dirp position dir Hlist
            set heading dir
            ask patch-here [set Qnew item dirp Qlist1];--find the value in Qlist with the same position as in the Hlist q old
        ]

        [;;Qchoice
            ask patch-here [set Qmax max Qlist1 ]
            let dir 0
            while [Qnew != Qmax]
              [
                ask patch-here [set dir one-of Hlist]
                set dirp position dir Hlist ;--find dir's position in the Hlist array
                set Qnew item dirp Qlist1 ;--find the value in Qlist with the same position as in the Hlist
              ]
              set heading dir
              ask patch-here [set Qnew item dirp Qlist1] ;--find the value in Qlist with the same position as in the Hlist q old
         ]


          ifelse [pcolor] of patch-ahead 5 != 82
            [
              print [pcolor] of patch-ahead 5
              let r [preward] of patch-ahead spacing
              set next-node nodes-on patch-ahead spacing
              set sum-rewards-per-episode  sum-rewards-per-episode + first r
              set reward-per-episode lput r reward-per-episode
              let QQnew max [Qlist1] of patch-ahead spacing
              set Qnew Qnew + step-size * (first  r +  discount * QQnew - Qnew )
              set Qnew precision Qnew 3
              ask patch-here [set Qlist1 replace-item dirp Qlist1 Qnew]
              ask maze-runners [ fd spacing ]
              color-link-green
            ]
            [;;affectation de reward directe
              let r [preward] of patch-ahead 5
              set sum-rewards-per-episode  sum-rewards-per-episode +  r
              set reward-per-episode lput r reward-per-episode
              ;;let QQnew max [Qlist1] of patch-ahead spacing
              ;;set Qnew Qnew + step-size * ( r +  discount * QQnew - Qnew )
              ;;set Qnew precision Qnew 3
              ask patch-here [set Qlist1 replace-item dirp Qlist1 r]
            ]
    ]
   ]
     let lng length reward-per-episode
     let lngsum sum reward-per-episode
     ;;set sum-rewards-per-episode sum-rewards-per-episode / lng
      if steps > 2000[set steps 2000]
    plotxy episode steps
    set-plot-pen-color green
    set episode episode + 1
      if exploration > 0 [set exploration exploration - decay]




  pen-erase]
 plot-pen-up]



update-plots



end



to q_epsilon
  set-current-plot "steps-per-episode"
set steps 0
  set-plot-pen-color white
  set exploration 0.02
  set Hlist [90 180 270 0]
  ;;set episode 1
  plot-pen-down
  let xx [pxcor] of patches with [pentrance = "entrance"]
  let yy  [pycor] of patches with [pentrance = "entrance"]
  ask maze-runners[while [episode <= num-episodes]
  [set steps 0
    ask links  [set color black set thickness 0 ]
    ask maze-runners [setxy  item 0 xx item 0 yy ]
    set sum-rewards-per-episode 0
    set reward-per-episode []
    pen-down
    let rand random-float 1
    ifelse rand <= exploration ;; episode d'exploration
    [print ("eploration ep")
      ask maze-runners [ while[  [preward] of patch-here != [100] ] ;; when episode ends
    [ set current-node nodes-on patch-here


        let Qnew 1
        let Qmax 0
        let dirp 0

       ifelse (num-possible-actions > 2) ;; decision node ekhtar random
          ;;------------------------------------------------------------------------------------------------------------------------------------
        [;;epsilon  action selection
              let dir one-of possible-actions  ;--pick random direction
              ask maze-runners[while [dir - heading = 180 or dir - heading  = -180 or dir = -1 ]
              [
                set dir one-of possible-actions ;--pick random direction
              ]
              set dirp position dir possible-actions
             ]
            set heading dir
            ask patch-here [set Qnew item dirp Qlist1]
         ]
            [
            ;;else ya pa de choix acrion = 1 ou 2
            ifelse ( num-possible-actions = 1 ) ;; impasse
            [
              let dir one-of possible-actions
              while  [dir  = -1 ]
              [
                set dir one-of possible-actions
              ]
              set heading dir
              set dirp position dir possible-actions
              ask patch-here [set Qnew item dirp Qlist1]

            ]
            [
              let dir one-of possible-actions
              while  [abs (dir - heading) = 180 or dir = -1] ;; la valeur elli ekhtarha bish traj3ou
              [set dir one-of possible-actions]
              set heading dir
              set dirp position dir possible-actions
              ask patch-here [set Qnew item dirp Qlist1]

            ]
          ]




              let r [preward] of patch-ahead spacing
              set next-node nodes-on patch-ahead spacing
              set sum-rewards-per-episode  sum-rewards-per-episode + first r
              set reward-per-episode lput r reward-per-episode
              let QQnew max [Qlist1] of patch-ahead spacing
              set Qnew Qnew + step-size * (first  r +  discount * QQnew - Qnew )
              set Qnew precision Qnew 3
              ask patch-here [set Qlist1 replace-item dirp Qlist1 Qnew]
              ask maze-runners [ fd spacing ]
              color-link-green


          set steps steps + 1
        ]
      ]]
      ;;else episode d'expoloitaton
      [
        print ("exploitation ep")
        ask maze-runners [ while[  [preward] of patch-here != [100] ] ;; when episode ends
    [
        set current-node nodes-on patch-here
        let Qnew 1
        let Qmax 0
        let dirp 0

        ask patch-here [set Qmax max Qlist1 ]
            let dir 0
            while [Qnew != Qmax]
              [
                ask patch-here [set dir one-of Hlist]
                set dirp position dir Hlist ;--find dir's position in the Hlist array
                set Qnew item dirp Qlist1 ;--find the value in Qlist with the same position as in the Hlist
              ]
              set heading dir
              ask patch-here [set Qnew item dirp Qlist1] ;--find the value in Qlist with the same position as in the Hlist q old
         ifelse [pcolor] of patch-ahead 5 != 82
            [

              let r [preward] of patch-ahead spacing
              set next-node nodes-on patch-ahead spacing
              set sum-rewards-per-episode  sum-rewards-per-episode + first r
              set reward-per-episode lput r reward-per-episode
              let QQnew max [Qlist1] of patch-ahead spacing
              set Qnew Qnew + step-size * (first  r +  discount * QQnew - Qnew )
              set Qnew precision Qnew 3
              ask patch-here [set Qlist1 replace-item dirp Qlist1 Qnew]
              ask maze-runners [ fd spacing ]
              color-link-green
            ]
            [;;affectation de reward directe
              let r [preward] of patch-ahead 5
              set sum-rewards-per-episode  sum-rewards-per-episode +  r
              set reward-per-episode lput r reward-per-episode
              ;;let QQnew max [Qlist1] of patch-ahead spacing
              ;;set Qnew Qnew + step-size * ( r +  discount * QQnew - Qnew )
              ;;set Qnew precision Qnew 3
              ask patch-here [set Qlist1 replace-item dirp Qlist1 r]
            ]
          set steps steps + 1
        ]
      ]

      ]
     let lng length reward-per-episode
     let lngsum sum reward-per-episode
     ;;set sum-rewards-per-episode sum-rewards-per-episode / lng

    plotxy episode steps
    set-plot-pen-color red
    set episode episode + 1




  pen-erase]
 plot-pen-up]



update-plots
end
to q_no_exploration
  reset-timer

  ;;set steps 0
  set-current-plot "steps-per-episode"
  set exploration 0
  set Hlist [90 180 270 0]
 ;; set episode 1
  plot-pen-down
  let xx [pxcor] of patches with [pentrance = "entrance"]
  let yy  [pycor] of patches with [pentrance = "entrance"]



set-plot-pen-color red

  ask maze-runners[
    while [episode <= num-episodes]
      [
        ask links  [set color black set thickness 0]
        ask maze-runners [setxy  item 0 xx item 0 yy ]
        set sum-rewards-per-episode 0
        set steps 0
        set reward-per-episode []
        let dir 0
        pen-down
        ask maze-runners [
          while[[preward] of patch-here != [100] ] ;; when episode ends
          [
            set current-node nodes-on patch-here
            let Qnew 1
            let Qmax 0
            let dirp 0
            ask patch-here [set Qmax max Qlist1 ]
            while [Qnew != Qmax]
              [
              ask patch-here [
              set dir one-of Hlist
              set dirp position dir Hlist
              set Qmax max Qlist1 ;--get max from the Qlist values of the current patch
              set Qnew item dirp Qlist1] ;--find the value in Qlist with the same position as in the Hlist
             ]
              set heading dir
            ifelse [pcolor] of patch-ahead 5 != 82
            [
              print [pcolor] of patch-ahead 5
              let r [preward] of patch-ahead spacing
              set next-node nodes-on patch-ahead spacing
              set sum-rewards-per-episode  sum-rewards-per-episode + first r
              set reward-per-episode lput r reward-per-episode
              let QQnew max [Qlist1] of patch-ahead spacing
              set Qnew Qnew + step-size * (first  r +  discount * QQnew - Qnew )
              set Qnew precision Qnew 3
              ask patch-here [set Qlist1 replace-item dirp Qlist1 Qnew]
              ask maze-runners [ fd spacing ]
              color-link-green
            ]
            [;;affectation de reward directe
              let r [preward] of patch-ahead 5
              set sum-rewards-per-episode  sum-rewards-per-episode +  r
              set reward-per-episode lput r reward-per-episode

              ;;let QQnew max [Qlist1] of patch-ahead spacing
              ;;set Qnew Qnew + step-size * ( r +  discount * QQnew - Qnew )
              ;;set Qnew precision Qnew 3
              ask patch-here [set Qlist1 replace-item dirp Qlist1 r]
            ]
            set steps steps + 1
        ] ]

          let lng length reward-per-episode
          let lngsum sum reward-per-episode
         ;; set sum-rewards-per-episode sum-rewards-per-episode / lng
          plotxy episode steps
          set-plot-pen-color red
          set episode episode + 1
          pen-erase
        ]

    plot-pen-up
    ]



    update-plots
end
to add-exit
  let inout-nodes nodes with [reward = -20]
  let po-exit one-of inout-nodes
  ask po-exit
          [
            set reward 100
            set maze-exit true set color red set size 5
            set label-color black set label "exit"
          ]
  ask patches [set preward [reward] of nodes-here  ]
  ask patches with [pcolor = 82] [set preward -200]




end







;;;;;;;;;;;;;;;;;;;;;;;;line to keep code in 80 columns;;;;;;;;;;;;;;;;;;;;;;;;
@#$#@#$#@
GRAPHICS-WINDOW
225
14
896
469
-1
-1
2.6414343
1
8
1
1
1
0
0
0
1
0
250
0
168
0
0
1
ticks
30.0

BUTTON
116
12
194
53
Setup
setup
NIL
1
T
OBSERVER
NIL
NIL
NIL
NIL
1

SLIDER
31
97
152
130
spacing
spacing
3
20
10.0
1
1
NIL
HORIZONTAL

BUTTON
13
10
91
51
Reset
reset-maze-runners
NIL
1
T
OBSERVER
NIL
NIL
NIL
NIL
1

SLIDER
31
129
152
162
debug
debug
0
2
0.0
1
1
NIL
HORIZONTAL

BUTTON
977
61
1106
94
NIL
q_epsilon\n
NIL
1
T
TURTLE
NIL
NIL
NIL
NIL
1

TEXTBOX
43
69
193
87
maze parameters
14
125.0
1

SLIDER
39
322
159
355
exploration
exploration
0
1
0.02
0.01
1
NIL
HORIZONTAL

SLIDER
39
354
159
387
step-size
step-size
0
1
0.95
0.05
1
NIL
HORIZONTAL

PLOT
915
216
1440
584
steps-per-episode
NIL
NIL
0.0
70.0
-20.0
10.0
true
true
"" ""
PENS
"sum rewards per ep" 1.0 0 -15040220 true "" "plotxy episode steps"

SLIDER
39
384
159
417
discount
discount
0
1
0.95
0.01
1
NIL
HORIZONTAL

BUTTON
978
125
1106
158
NIL
q_epsilon_decay\n
NIL
1
T
TURTLE
NIL
NIL
NIL
NIL
1

TEXTBOX
1369
476
1519
518
                0\n        270        90\n               180
11
86.0
1

BUTTON
978
93
1106
126
NIL
q_no_exploration
NIL
1
T
TURTLE
NIL
NIL
NIL
NIL
1

SLIDER
39
416
159
449
decay
decay
0
1
0.02
0.01
1
NIL
HORIZONTAL

TEXTBOX
1009
27
1159
45
Algorithms 
14
125.0
1

BUTTON
58
204
136
237
NIL
add-exit\n
NIL
1
T
OBSERVER
NIL
NIL
NIL
NIL
1

SLIDER
42
281
162
314
num-episodes
num-episodes
0
1000
210.0
10
1
NIL
HORIZONTAL

@#$#@#$#@
## WHAT IS IT?


A NetLog script written for version 6.1.1. A model of agent trying to find exit. 

## HOW IT WORKS

As a initial step it generates a maze according to the spacing set by the slider. It creates a network with nodes along the path. 
After agent try to find exit. At every hub it colors the path according to history. 
Green if it's the shortest path from the entrance. 
Yellow in case it still has to very if there is a blind spot at the end of the road. 
Red for those path who takes nowhere. 
In the meanwhile agent explores the world some monitors and a plot show statistics on the left side of the interface. 

## HOW TO USE IT

Press "Setup" to start
Press "Find exit" to make agent find exit 
Press "Find exit step-by-step to make agent stop after each node.


## THINGS TO NOTICE

Pay attention of how agent comes back when it finds a blind spot. 

## THINGS TO TRY

Adjust the spacing to create smaller or bigger maze.
Set debug to 1 or 2 in order to print a logger. 

## EXTENDING THE MODEL

Algorithm takes into accounts that more agents could explore the maze at the same time in order to find exit faster. Future versions could support the creation of more maze runners.
A future study could create a second maze runner that take into accounts path already explored by the first maze runner. A cost function could estimate the perfect time the second maze runner needs to wait to find exit faster. 

## RELATED MODELS

This work is based on script created as a case study for a the graduation thesis: "Cooperative and optimization strategies in bio-based agents model" by C. Crespi and A. Rapisarda, A. Pluchino as supervisor. 

## CREDITS AND REFERENCES

NetLogo model developed by R. Rotondo (riccardo.rotondo@phd.unict.it) as an assignment of a PhD course. 
A copy, along with some documentation and screenshots, is available on github at: https://github.com/rrotondo/maze-escape 
@#$#@#$#@
default
true
0
Polygon -7500403 true true 150 5 40 250 150 205 260 250

airplane
true
0
Polygon -7500403 true true 150 0 135 15 120 60 120 105 15 165 15 195 120 180 135 240 105 270 120 285 150 270 180 285 210 270 165 240 180 180 285 195 285 165 180 105 180 60 165 15

arrow
true
0
Polygon -7500403 true true 150 0 0 150 105 150 105 293 195 293 195 150 300 150

box
false
0
Polygon -7500403 true true 150 285 285 225 285 75 150 135
Polygon -7500403 true true 150 135 15 75 150 15 285 75
Polygon -7500403 true true 15 75 15 225 150 285 150 135
Line -16777216 false 150 285 150 135
Line -16777216 false 150 135 15 75
Line -16777216 false 150 135 285 75

bug
true
0
Circle -7500403 true true 96 182 108
Circle -7500403 true true 110 127 80
Circle -7500403 true true 110 75 80
Line -7500403 true 150 100 80 30
Line -7500403 true 150 100 220 30

butterfly
true
0
Polygon -7500403 true true 150 165 209 199 225 225 225 255 195 270 165 255 150 240
Polygon -7500403 true true 150 165 89 198 75 225 75 255 105 270 135 255 150 240
Polygon -7500403 true true 139 148 100 105 55 90 25 90 10 105 10 135 25 180 40 195 85 194 139 163
Polygon -7500403 true true 162 150 200 105 245 90 275 90 290 105 290 135 275 180 260 195 215 195 162 165
Polygon -16777216 true false 150 255 135 225 120 150 135 120 150 105 165 120 180 150 165 225
Circle -16777216 true false 135 90 30
Line -16777216 false 150 105 195 60
Line -16777216 false 150 105 105 60

car
false
0
Polygon -7500403 true true 300 180 279 164 261 144 240 135 226 132 213 106 203 84 185 63 159 50 135 50 75 60 0 150 0 165 0 225 300 225 300 180
Circle -16777216 true false 180 180 90
Circle -16777216 true false 30 180 90
Polygon -16777216 true false 162 80 132 78 134 135 209 135 194 105 189 96 180 89
Circle -7500403 true true 47 195 58
Circle -7500403 true true 195 195 58

circle
false
0
Circle -7500403 true true 0 0 300

circle 2
false
0
Circle -7500403 true true 0 0 300
Circle -16777216 true false 30 30 240

cow
false
0
Polygon -7500403 true true 200 193 197 249 179 249 177 196 166 187 140 189 93 191 78 179 72 211 49 209 48 181 37 149 25 120 25 89 45 72 103 84 179 75 198 76 252 64 272 81 293 103 285 121 255 121 242 118 224 167
Polygon -7500403 true true 73 210 86 251 62 249 48 208
Polygon -7500403 true true 25 114 16 195 9 204 23 213 25 200 39 123

cylinder
false
0
Circle -7500403 true true 0 0 300

dot
false
0
Circle -7500403 true true 90 90 120

face happy
false
0
Circle -7500403 true true 8 8 285
Circle -16777216 true false 60 75 60
Circle -16777216 true false 180 75 60
Polygon -16777216 true false 150 255 90 239 62 213 47 191 67 179 90 203 109 218 150 225 192 218 210 203 227 181 251 194 236 217 212 240

face neutral
false
0
Circle -7500403 true true 8 7 285
Circle -16777216 true false 60 75 60
Circle -16777216 true false 180 75 60
Rectangle -16777216 true false 60 195 240 225

face sad
false
0
Circle -7500403 true true 8 8 285
Circle -16777216 true false 60 75 60
Circle -16777216 true false 180 75 60
Polygon -16777216 true false 150 168 90 184 62 210 47 232 67 244 90 220 109 205 150 198 192 205 210 220 227 242 251 229 236 206 212 183

fish
false
0
Polygon -1 true false 44 131 21 87 15 86 0 120 15 150 0 180 13 214 20 212 45 166
Polygon -1 true false 135 195 119 235 95 218 76 210 46 204 60 165
Polygon -1 true false 75 45 83 77 71 103 86 114 166 78 135 60
Polygon -7500403 true true 30 136 151 77 226 81 280 119 292 146 292 160 287 170 270 195 195 210 151 212 30 166
Circle -16777216 true false 215 106 30

flag
false
0
Rectangle -7500403 true true 60 15 75 300
Polygon -7500403 true true 90 150 270 90 90 30
Line -7500403 true 75 135 90 135
Line -7500403 true 75 45 90 45

flower
false
0
Polygon -10899396 true false 135 120 165 165 180 210 180 240 150 300 165 300 195 240 195 195 165 135
Circle -7500403 true true 85 132 38
Circle -7500403 true true 130 147 38
Circle -7500403 true true 192 85 38
Circle -7500403 true true 85 40 38
Circle -7500403 true true 177 40 38
Circle -7500403 true true 177 132 38
Circle -7500403 true true 70 85 38
Circle -7500403 true true 130 25 38
Circle -7500403 true true 96 51 108
Circle -16777216 true false 113 68 74
Polygon -10899396 true false 189 233 219 188 249 173 279 188 234 218
Polygon -10899396 true false 180 255 150 210 105 210 75 240 135 240

house
false
0
Rectangle -7500403 true true 45 120 255 285
Rectangle -16777216 true false 120 210 180 285
Polygon -7500403 true true 15 120 150 15 285 120
Line -16777216 false 30 120 270 120

leaf
false
0
Polygon -7500403 true true 150 210 135 195 120 210 60 210 30 195 60 180 60 165 15 135 30 120 15 105 40 104 45 90 60 90 90 105 105 120 120 120 105 60 120 60 135 30 150 15 165 30 180 60 195 60 180 120 195 120 210 105 240 90 255 90 263 104 285 105 270 120 285 135 240 165 240 180 270 195 240 210 180 210 165 195
Polygon -7500403 true true 135 195 135 240 120 255 105 255 105 285 135 285 165 240 165 195

line
true
0
Line -7500403 true 150 0 150 300

line half
true
0
Line -7500403 true 150 0 150 150

pentagon
false
0
Polygon -7500403 true true 150 15 15 120 60 285 240 285 285 120

person
false
0
Circle -7500403 true true 110 5 80
Polygon -7500403 true true 105 90 120 195 90 285 105 300 135 300 150 225 165 300 195 300 210 285 180 195 195 90
Rectangle -7500403 true true 127 79 172 94
Polygon -7500403 true true 195 90 240 150 225 180 165 105
Polygon -7500403 true true 105 90 60 150 75 180 135 105

plant
false
0
Rectangle -7500403 true true 135 90 165 300
Polygon -7500403 true true 135 255 90 210 45 195 75 255 135 285
Polygon -7500403 true true 165 255 210 210 255 195 225 255 165 285
Polygon -7500403 true true 135 180 90 135 45 120 75 180 135 210
Polygon -7500403 true true 165 180 165 210 225 180 255 120 210 135
Polygon -7500403 true true 135 105 90 60 45 45 75 105 135 135
Polygon -7500403 true true 165 105 165 135 225 105 255 45 210 60
Polygon -7500403 true true 135 90 120 45 150 15 180 45 165 90

sheep
false
15
Circle -1 true true 203 65 88
Circle -1 true true 70 65 162
Circle -1 true true 150 105 120
Polygon -7500403 true false 218 120 240 165 255 165 278 120
Circle -7500403 true false 214 72 67
Rectangle -1 true true 164 223 179 298
Polygon -1 true true 45 285 30 285 30 240 15 195 45 210
Circle -1 true true 3 83 150
Rectangle -1 true true 65 221 80 296
Polygon -1 true true 195 285 210 285 210 240 240 210 195 210
Polygon -7500403 true false 276 85 285 105 302 99 294 83
Polygon -7500403 true false 219 85 210 105 193 99 201 83

square
false
0
Rectangle -7500403 true true 30 30 270 270

square 2
false
0
Rectangle -7500403 true true 30 30 270 270
Rectangle -16777216 true false 60 60 240 240

star
false
0
Polygon -7500403 true true 151 1 185 108 298 108 207 175 242 282 151 216 59 282 94 175 3 108 116 108

target
false
0
Circle -7500403 true true 0 0 300
Circle -16777216 true false 30 30 240
Circle -7500403 true true 60 60 180
Circle -16777216 true false 90 90 120
Circle -7500403 true true 120 120 60

tree
false
0
Circle -7500403 true true 118 3 94
Rectangle -6459832 true false 120 195 180 300
Circle -7500403 true true 65 21 108
Circle -7500403 true true 116 41 127
Circle -7500403 true true 45 90 120
Circle -7500403 true true 104 74 152

triangle
false
0
Polygon -7500403 true true 150 30 15 255 285 255

triangle 2
false
0
Polygon -7500403 true true 150 30 15 255 285 255
Polygon -16777216 true false 151 99 225 223 75 224

truck
false
0
Rectangle -7500403 true true 4 45 195 187
Polygon -7500403 true true 296 193 296 150 259 134 244 104 208 104 207 194
Rectangle -1 true false 195 60 195 105
Polygon -16777216 true false 238 112 252 141 219 141 218 112
Circle -16777216 true false 234 174 42
Rectangle -7500403 true true 181 185 214 194
Circle -16777216 true false 144 174 42
Circle -16777216 true false 24 174 42
Circle -7500403 false true 24 174 42
Circle -7500403 false true 144 174 42
Circle -7500403 false true 234 174 42

turtle
true
0
Polygon -10899396 true false 215 204 240 233 246 254 228 266 215 252 193 210
Polygon -10899396 true false 195 90 225 75 245 75 260 89 269 108 261 124 240 105 225 105 210 105
Polygon -10899396 true false 105 90 75 75 55 75 40 89 31 108 39 124 60 105 75 105 90 105
Polygon -10899396 true false 132 85 134 64 107 51 108 17 150 2 192 18 192 52 169 65 172 87
Polygon -10899396 true false 85 204 60 233 54 254 72 266 85 252 107 210
Polygon -7500403 true true 119 75 179 75 209 101 224 135 220 225 175 261 128 261 81 224 74 135 88 99

wheel
false
0
Circle -7500403 true true 3 3 294
Circle -16777216 true false 30 30 240
Line -7500403 true 150 285 150 15
Line -7500403 true 15 150 285 150
Circle -7500403 true true 120 120 60
Line -7500403 true 216 40 79 269
Line -7500403 true 40 84 269 221
Line -7500403 true 40 216 269 79
Line -7500403 true 84 40 221 269

wolf
false
0
Polygon -16777216 true false 253 133 245 131 245 133
Polygon -7500403 true true 2 194 13 197 30 191 38 193 38 205 20 226 20 257 27 265 38 266 40 260 31 253 31 230 60 206 68 198 75 209 66 228 65 243 82 261 84 268 100 267 103 261 77 239 79 231 100 207 98 196 119 201 143 202 160 195 166 210 172 213 173 238 167 251 160 248 154 265 169 264 178 247 186 240 198 260 200 271 217 271 219 262 207 258 195 230 192 198 210 184 227 164 242 144 259 145 284 151 277 141 293 140 299 134 297 127 273 119 270 105
Polygon -7500403 true true -1 195 14 180 36 166 40 153 53 140 82 131 134 133 159 126 188 115 227 108 236 102 238 98 268 86 269 92 281 87 269 103 269 113

x
false
0
Polygon -7500403 true true 270 75 225 30 30 225 75 270
Polygon -7500403 true true 30 75 75 30 270 225 225 270
@#$#@#$#@
NetLogo 6.1.1
@#$#@#$#@
@#$#@#$#@
@#$#@#$#@
@#$#@#$#@
@#$#@#$#@
default
0.0
-0.2 0 0.0 1.0
0.0 1 1.0 0.0
0.2 0 0.0 1.0
link direction
true
0
Line -7500403 true 150 150 90 180
Line -7500403 true 150 150 210 180
@#$#@#$#@
0
@#$#@#$#@
