# motus-metadata-history

This repo tracks changes to the metadata used for tag detections by
the motus server.  Each time the cached metadata are updated on the
data-processing server, the changes are checked-in to this repo as a
new commit.  Every time a batch of data is processed, the current
commit hashes for these metadata are recorded in the `batchParams`
table.  This is part of what is needed to let us reconstruct exactly
how data were processed and why.

Changes to metadata are made via motus.org, and eventually reflected
here.  Commits to this repo are automatically generated by the server
when it updates metadata, so no manual changes should be made to
these files.  The format is .csv so that it is easy to visualize
changes over time.

## Files: ##

### tags.csv ###
Sorted by tagID:
 - tagID; integer unique motus tag ID
 - manufacturer
 - model
 - codeSet; for coded ID tags
 - nomFreq; nominal tag frequency, in MHz
 - mfgID; manufacturer ID (usually printed on tag)
 - period; repetition frequency, in seconds

### tag_deployments.csv ###
Sorted by tagID, tsStart:
 - tagID; integer unique motus tag ID
 - projectID; integer unique motus project ID
 - tsStart; unix timestamp of tag deployment start
 - tsEnd; unix timestamp of end of deployment
 - tsStartCode; method for calculating start date, the first of these which was possible:
     1. `tsStart`, the starting date for a tag deployment record
     2. `dateBin`, the start of the quarter year in which the tag was expected to be deployed
     3. `ts`, the date the tag was registered
 - tsEndCode; method for calculating end date, the first of these which was possible:
     1. `tsEnd`, the ending date for a tag deployment; e.g. if a tag was found, or manually deactivated
     2. `tsStart`, for a different deployment of the same tag
     3. `tsStart + predictTagLifespan(model, BI) * marginOfError`, if the tag model is known
     4. `tsStart + predictTagLifespan(guessTagModel(speciesID), BI) * marginOfError` , if the species is known
     5. 90 days if no other information is available

### parameter_overrides.csv ###
Sorted by projectID, serno, tsStart
 - projectID; integer unique motus project ID
 - serno; serial number of device involved in this event (if any)
 - tsStart; starting timestamp for this override (Lotek only)
 - tsEnd; ending timestamp for this override (Lotek only)
 - monoBNhigh; ending boot session for this override (SG only)
 - monoBNlow; starting boot session for this override (SG only)
 - progName; identifier of program; e.g. 'find_tags'
 - paramName; name of parameter (e.g. 'minFreq')
 - paramVal; value of parameter
 - why TEXT; human-readable reason for this override
