# XcodeMultiProjectWorkspace

https://youtu.be/AfCGqH4tLV4
<img width="824" alt="スクリーンショット_2022_09_24_14_15" src="https://user-images.githubusercontent.com/47273077/192081201-40673982-8b50-49a7-93ef-f8579c2670b8.png">

```swift

import UIKit
import DesignKit
import NetworkKit
import NetworkKitV2

class ViewController: UIViewController {
    
    private let designKit: DesignKit = DesignKitImp()
    private let networkKit = NetworkKitImp()
    
    private lazy var redView: UIView = {
        return designKit.buildView(themeColor: .bgColor1)
    }()

    override func viewDidLoad() {
        super.viewDidLoad()
        setupViews()
        fetchUsers()
    }

    private func setupViews() {
        [redView].forEach(view.addSubview(_:))
        // [redView, UIView(), UIView()].forEach(view.addSubview(_:))
        //        view.addSubview(redView)
        
        NSLayoutConstraint.activate([
            redView.heightAnchor.constraint(equalToConstant: 200),
            redView.widthAnchor.constraint(equalToConstant: 200),
            redView.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            redView.centerYAnchor.constraint(equalTo: view.centerYAnchor)
        ])
    }
    
    private func fetchUsers() {
        Task {
            do {
                let url = URL(string: "https://jsonplaceholder.typicode.com/users")!
                let users: [User] = try await networkKit.get(url: url)
                print(users.first)
            } catch {
                
            }
            

        }
    }

}

struct User: Decodable {
    let name: String
    let email: String
}

```

```swift
import UIKit

public protocol DesignKit {
    func buildRedView() -> UIView
    func buildView(themeColor: ThemeColor) -> UIView
}

public enum ThemeColor {
    case bgColor1
    case bgColor2
    
    var color: UIColor {
        switch self {
        case .bgColor1:
            return .systemIndigo
        case .bgColor2:
            return .systemGray
        }
    }
}

public class DesignKitImp: DesignKit {
    
    public init() {}
    
    public func buildRedView() -> UIView {
        let view = UIView()
        view.backgroundColor = .red
        view.translatesAutoresizingMaskIntoConstraints = false
        return view
    }
    
    public func buildView(themeColor: ThemeColor) -> UIView {
        let view = UIView()
        view.backgroundColor = themeColor.color
        view.translatesAutoresizingMaskIntoConstraints = false
        return view
    }
    
}

```

```swift
import Foundation

public class NetworkKitImp {
    
    public init() {}
    
    public func get<T: Decodable>(url: URL) async throws -> T {
        let (data, _) = try await URLSession.shared.data(from: url)
        let decoder = JSONDecoder()
        return try decoder.decode(T.self, from: data)
    
    }
    
}
```
