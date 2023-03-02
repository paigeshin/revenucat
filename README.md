# revenucat

```swift

import UIKit
import Purchases

class ViewController: UIViewController {

    private let imageView: UIImageView = {
        let imageView = UIImageView()
        imageView.image = UIImage(systemName: "crown.fill")
        imageView.tintColor = .systemPink
        imageView.contentMode = .scaleAspectFit
        return imageView
    }()
    
    private let subscribeButton: UIButton = {
       let button = UIButton()
        button.backgroundColor = .systemGreen
        button.setTitle("Subscribe", for: .normal)
        button.setTitleColor(.white, for: .normal)
        button.isHidden = true
        return button
    }()
    
    private let restoreButton: UIButton = {
       let button = UIButton()
        button.setTitle("Restore", for: .normal)
        button.setTitleColor(.link, for: .normal)
        button.isHidden = true
        return button
    }()
    
    private let label: UILabel = {
        let label = UILabel()
        label.text = "You are subscribed!"
        label.numberOfLines = 0
        label.textAlignment = .center
        label.font = .systemFont(ofSize: 32)
        label.isHidden = true
        return label
    }()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.view.addSubview(self.imageView)
        self.view.addSubview(self.subscribeButton)
        self.view.addSubview(self.restoreButton)
        self.view.addSubview(self.label)
        self.subscribeButton.addTarget(self, action: #selector(didTapSubscribe), for: .touchUpInside)
        self.restoreButton.addTarget(self, action: #selector(didTapRestore), for: .touchUpInside)
        self.setup()
    }
    
    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()
        let size: CGFloat = self.view.frame.size.width / 1.5
        self.imageView.frame = CGRect(x: (self.view.frame.size.width - size) / 2,
                                      y: self.view.safeAreaInsets.top + 100,
                                      width: size,
                                      height: size)
        
        self.label.frame = CGRect(x: (self.view.frame.size.width - 220) / 2,
                                      y: self.view.safeAreaInsets.top + 100 + size + 10,
                                      width: 220,
                                      height: 100)
        
        self.subscribeButton.frame = CGRect(x: (self.view.frame.size.width - 220) / 2,
                                      y: self.view.safeAreaInsets.top + 100 + size + 10,
                                      width: 220,
                                      height: 50)
        
        self.restoreButton.frame = CGRect(x: (self.view.frame.size.width - 220) / 2,
                                      y: self.view.safeAreaInsets.top + 100 + size + 70,
                                      width: 220,
                                      height: 50)
        
    }
    
    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        
    }
    
    @objc func didTapSubscribe() {
        self.fetchPakcage { [weak self] package in
            self?.purchase(package: package)
        }
    }
    
    @objc func didTapRestore() {
        
    }
    
    func setup() {
        Purchases.shared.purchaserInfo { [weak self] info, error in
            guard let info = info, error == nil else {
                return
            }
            
            if info.entitlements.all["Premium"]?.isActive == true {
                DispatchQueue.main.async {
                    self?.label.isHidden = false
                }
            } else {
                DispatchQueue.main.async {
                    self?.subscribeButton.isHidden = false
                    self?.restoreButton.isHidden = false
                }
            }
            
        }
    }

    func fetchPakcage(completion: @escaping(Purchases.Package) -> Void) {
        Purchases.shared.offerings { offerings, error in
            guard let offerings = offerings, error == nil else {
                return
            }
            
            guard let package = offerings.all.first?.value.availablePackages.first else {
                return
            }
            completion(package)
            
        }
    }
    
    func purchase(package: Purchases.Package) {
        Purchases.shared.purchasePackage(package) { transaction, info, error, userCancelled in
            guard let transaction, let info, error == nil, !userCancelled else {
                return
            }
            print(transaction.transactionState)
            print(info.entitlements)
        }
    }
    
    func restorePurhcases() {
        Purchases.shared.restoreTransactions { info, error in
            guard let info, error == nil else {
                return
            }
            print(info.entitlements)
        }
    }

}


```
