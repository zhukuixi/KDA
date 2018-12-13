# KDA
fix the bugs in the original KDA codes

 Bug 1: (early terminate network expandsion)
 Importance: minor
 Bug place: findNLayerNeighborsLinkPairs()
 Description: In directed=FALSE mode, it will find downstream first and then find upstream members. However,
 it will terminate and return NULL if  we got no  downstream from the seed lists, instead of trying to find 
 their upstream members. Fortunately, this bug will only happen when all the current seed list in network 
 expansion are leaf nodes.
 
  PS: After fixing this bug, if we do directed=FALSE mode for network A->B and B->A, we will gain same result now.




  Bug 2: (For A->B, B->A loop, it literally will only keep the edge appear first)
  Importance: serious
  Bug place: removeDuplicatedLinks()
  Description: This function serves to remove duplicate edges. If directed=FALSE, for A->B, B->A loop, it would 
  keep the one appeared first in the table. This explain those equivalent structure nodes can be KD and no-KD at the same time.



  Bug 3:(obtain all the edges containing a list of nodes or obtain all the edges from network expansion) 
  Importance: serious
  Bug place: getSubnetwork_LinkPairs()
  Description: 
  In the network expasion code, there are 2 judgement about "nlayers==1".
  #################### START OF CDOE #######
   if ( isTRUE( all.equal( nlayers , 1 ) ) ) ## Part1 : If we nlayers==1, will return the network expansion result.
   {
      return ( merged )
   }

   # get nodes   
   ineighbors <- union( merged[,1] , merged[,2] )

   if (nlayers==1){                                                  ##Part2: It seems like we can never visit this part
      res=getSubnetwork_LinkPairs(linkpairs, subnetNodes=ineighbors)  ## obtain all the edges containing a list of nodes from the network expansion
      return (res)
   }
   ##################### END OF CODE ########
  These 2 parts will make huge difference. The first part "merged" should be equal to pathFinder (if we corrected those bugs). 
  The second part is a different story. If we use part1, BUG2 will take its effect. If we use part2, BUG2 is no more 
  a problem but it is a completely different idea. 
