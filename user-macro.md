## Macro title: Incoming Links
## Macro has a body: Y
## Body processing: 
## Output: 
##
## Developed by: Owen Conti with minor changes by Lars Strudsholm
## Date created: 21/03/2018
## Installed by: Owen Conti/Lars Strudsholm
## @param IncomingLinkLabel:title=Incoming Link Label|type=string|desc=Label used to filter the incoming links.|required=false
## @param RequiresAllLabels:title=Requires All Labels|type=boolean|desc=If true, all labels specified must be present.|required=false
## @param IncomingLinkExclusionLabel:title=Incoming Link Exclusion Label|type=string|desc=Label used to filter the incoming links to exclude.|required=false
## @param RequiresAllLabelsToExclude:title=Exclusion Requires All Labels|type=boolean|desc=If true, all labels specified must be present to exclude the link.|required=false
## @param WhatToDisplay:title=What To Display|type=enum|enumValues=links,list,count|default=list|desc=Decide what to display
#set($displayCount=0)

##incomingLinkIsExcluded determines if exclusion checks remove the link from being displayed, regardless of other checks
#set( $incomingLinkIsExcluded=false )

## Split the exclusion labels variable by comma and count how many labels we have to match against
#set( $exclusionLabels = $paramIncomingLinkExclusionLabel.replaceAll(" ", "").split(",") )
#set( $exclusionLabelsCount = 0 )
#foreach( $exclusionLabel in $exclusionLabels)
    #set($exclusionLabel=$exclusionLabel.replaceAll(" ",""))
    #if($exclusionLabel.length() > 0)
        #set( $exclusionLabelsCount = $exclusionLabelsCount + 1 )
    #end
#end
        
## Split the labels variable by comma and count how many labels we have to match against
#set( $matchingLabels = $paramIncomingLinkLabel.replaceAll(" ", "").split(",") )
#set( $matchingLabelsCount = 0 )
#foreach( $matchingLabel in $matchingLabels)
    #set($matchingLabel=$matchingLabel.replaceAll(" ",""))
    #if($matchingLabel.length() > 0)
        #set( $matchingLabelsCount = $matchingLabelsCount + 1 )
    #end
#end
#if($paramWhatToDisplay.equals("links"))
#elseif($paramWhatToDisplay.equals("list"))
<ul>
#elseif($paramWhatToDisplay.equals("count"))
##end if what to display
#end
#foreach ($bla in $action.getIncomingLinks())
    #if( !($bla.getSourceContent().isDeleted()) )
        #set( $incomingLinkIsExcluded=false )
        #set( $exclusionCount = 0 )
        #set( $pageLabels = $bla.getSourceContent().getLabels() )
        #set( $hasMatchingLabel = false )
        #set( $matchCount = 0 )
        #foreach( $matchingLabel in $matchingLabels)
            #set($matchingLabel=$matchingLabel.replaceAll(" ",""))
            
            #foreach( $label in $pageLabels ) 
                #if( $label.getName().equals( $matchingLabel ) )
                    #set( $matchCount = $matchCount + 1 )
                #end
            #end
        #end
         #foreach( $exclusionLabel in $exclusionLabels)
            #set($exclusionLabel=$exclusionLabel.replaceAll(" ",""))
            
            #foreach( $label in $pageLabels ) 
                #if( $label.getName().equals( $exclusionLabel ) )
                    #set( $exclusionCount = $exclusionCount + 1 )
                #end
            #end
        #end
        #if( $paramRequiresAllLabels == true && $matchCount == $matchingLabelsCount )
            #set( $hasMatchingLabel = true )
        #end
        #if ( $paramRequiresAllLabels == false && ($matchCount > 0 || $matchingLabelsCount == 0) )
            #set( $hasMatchingLabel = true )
        #end
        #if( $paramRequiresAllLabelsToExclude == true && $exclusionCount == $exclusionLabelsCount )
            #set( $incomingLinkIsExcluded = true )
        #end
        #if ( $paramRequiresAllLabelsToExclude == false && ($exclusionCount > 0 && $exclusionLabelsCount > 0) )
            #set( $incomingLinkIsExcluded = true )
        #end
        #if( $hasMatchingLabel && $incomingLinkIsExcluded==false )
            #set($displayCount=$displayCount+1)
            #if($paramWhatToDisplay.equals("links"))
<a href="$bla.getSourceContent().getUrlPath()">$bla.getSourceContent().getTitle()</a>
<br>
            #elseif($paramWhatToDisplay.equals("list"))
<li><a href="$bla.getSourceContent().getUrlPath()">$bla.getSourceContent().getTitle()</a></li>
            ##end if what to display
            #end
        ##end if to display
        #end
    ## end if not deleted
    #end
## end foreach 
#end
#if($paramWhatToDisplay.equals("links"))
    #if($displayCount==0)
None
    #end
#elseif($paramWhatToDisplay.equals("list"))
    #if($displayCount==0)
<li>None</li>
    #end
</ul>
#elseif($paramWhatToDisplay.equals("count"))
    #if($displayCount==1)
Found $displayCount Link.
    #else
Found $displayCount Links.
    #end
##end if to display count
#end