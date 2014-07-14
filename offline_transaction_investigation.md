# Offline Transaction Investigation

### Useful APIs

__FileUtils.m__ handles file path and related work. We can use it to find the cacheDirectory or create a directory.

## How to get cache on file system

For now, I think we can use these two classes:

__TransPendingListModel__ and __TransPendingListEntry__

## APIs may be useful

**TransPendingListEntry**:

* _getPendingStorePath_

**TransPendingListModel**:

* _getFirstPendingEntryID: withName:_  The parameter is reference which is used for record the document id and name. We can use this method to get the document ID and filename 



