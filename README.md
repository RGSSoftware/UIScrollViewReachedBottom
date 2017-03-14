# UIScrollViewReachedBottom
An example showing how to propagate UIScrollView did ReachedBottom events.

## RxSwift
```swift
import UIKit
import RxSwift
import RxCocoa

extension Reactive where Base: UIScrollView {
    var reachedBottom: ControlEvent<Void> {
        let observable = contentOffset
            .flatMap { [weak base] contentOffset -> Observable<Void> in
                guard let scrollView = base else {
                    return Observable.empty()
                }

                let visibleHeight = scrollView.frame.height - scrollView.contentInset.top - scrollView.contentInset.bottom
                let y = contentOffset.y + scrollView.contentInset.top
                let threshold = max(0.0, scrollView.contentSize.height - visibleHeight)

                return y > threshold ? Observable.just() : Observable.empty()
            }

        return ControlEvent(events: observable)
    }
}


//caller
self.viewModel.nextPageTrigger = self.collectionView
    .rx
    .reachedBottom
    .asDriver()
```

## UIScrollView
```swift
func scrollViewDidScroll(scrollView: UIScrollView) {
    let  height = scrollView.frame.size.height
    let contentYoffset = scrollView.contentOffset.y
    let distanceFromBottom = scrollView.contentSize.height - contentYoffset
    if distanceFromBottom < height {
        print(" you reached end of the table")
    }
}
```
