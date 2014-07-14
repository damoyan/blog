# Sort in Android

Start in `GridCellView`

`mLongPressed` will run `showMagnifyGlass()`

this function will call `GridViewerController.showMagnifyGlass(,,)` method

this function will create a `GridActionMenuActivity`

this activity will call `showInfoView()` to create a `GridMagnifyGlassSortView`

this view will call `GridActionMenuActivity.performSort()` to handle sort event

this function will new an `Intent` and return it to `DocumentViewerActivity.onAcitivityResult`

the `onActivityResult` will handle the information