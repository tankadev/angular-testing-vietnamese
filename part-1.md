##### Tác giả: Adam Morgan
##### Link bài viết [Testing Angular with Jasmine and Karma (Part 1)](https://www.digitalocean.com/community/tutorials/testing-angular-with-jasmine-and-karma-part-1)
##### Ngày dịch: 28/10/2020

## Mục tiêu

Trong bài hướng dẫn này chúng ta sẽ xây dựng và kiểm thử một thư mục của nhân viên cho một công ty không có thật. Thư mục này sẽ hiển thị toàn bộ danh sách của người dùng cùng với một màn hình khác để xem thông tin cá nhân của những người dùng. Trong phần hướng dẫn này chúng ra sẽ tập trung xây dựng `service`, kiểm thử service này và sẽ sử dụng cho những người dùng.

![Giao diện thư mục nhân viên](assets/part1-user-directory.png)

Trong những phần hướng dẫn tiếp theo, chúng ta sẽ thêm vào trang hồ sơ người dùng với hình ảnh Pokemon yêu thích của người dùng sử dụng [Pokeapi](https://pokeapi.co/) và tìm hiểu thêm cách kiểm thử service cho HTTP request.

## Bạn nên biết điều gì

Trọng tâm chính của bài hướng dẫn này là kiểm thử các giả định của tôi mà bạn sẽ làm việc với TypeScript và ứng dụng Angular. Như mục tiêu của bài hướng dẫn này thì tôi sẽ không dành thời gian để giải thích service là gì và cách sử dụng của nó. Tôi sẽ cung cấp cho bạn code cũng như cách làm qua các phần kiểm thử của chúng ta.

## Tại sao phải Test ?

Từ kinh nghiệm cá nhân, kiểm thử là cách tốt nhất để ngăn chặn những khiếm khuyết của phần mềm. Tôi đã làm việc trong rất nhiều nhóm trong quá khứ, khi có những phần code được cập nhật và lập trình viên sẽ mở trình duyệt của họ hoặc Postman để kiểm tra xem các tính năng vẫn còn chạy không. Cách tiệp cần này (kiểm thử thủ công) là một thảm hoạ.

> Kiểm thử là cách tốt nhất để ngăn chặn những khiếm khuyết của phần mềm

Khi những tính năng và code tăng lên, kiểm thử thủ công sẽ trở nên tốn kém, tốn nhiều thời gian và dễ bị lỗi. Nếu một tính năng hoặc chức năng được gỡ bỏ thì liệu lập trình viên có nhớ hết tất cả những nơi mà nó được sử dụng hay không? Tất cả những lập trình trình viên có kiểm thử thủ công theo cùng một cách hay không ? Có lẻ là không.

Lý do mà chúng ta kiểm tra lại code là để xác nhận lại rằng nó vẫn hoạt động như chúng ta mong đợi hay không. Kết quả của quá trình này bạn sẽ tìm được cho bạn một tính năng tốt hơn và những lập trình viên khác cũng hỗ trợ thiết kế cho các API của bạn.

## Tại sao là Karma ?

Karma là một sản phẩm của nhóm AngularJS để cạnh tranh với những công cụ hiện có để kiểm thử những tính năng cho framework của chính họ. Như kết quả của nó, họ làm ra Karma và đã chuyển đổi nó tới Angular như một test runner mặc định khi tạo mới ứng dụng với Angular CLI.

Ngoài việc nó hoạt động tốt với Angular, nó cũng cung cấp một cách linh hoạt để điều chỉnh Karma phù hợp với quy trình làm việc của bạn. Nó bao gồm những tuỳ chọn để kiểm thử code của bạn trên nhiều trình duyệt và thiết bị như điện thoại, tablet, và thậm chí cả PS3 giống như Youtube.

Karma cũng cung cấp cho bạn tuỳ chọn để thay thế Jasmine với những framework kiểm thử khác như [Mocha](https://mochajs.org/) và [QUnit](https://qunitjs.com/) hoặc tích hợp với nhiều dịch vụ CI(Continuous integration) như [Jenkins](https://jenkins.io/), [TravisCI](https://travis-ci.org/) hoặc [CircleCI](https://circleci.com/).

Trừ khi bạn bổ sung thêm một số cấu hình nếu không thì thông thường cách tương tác với Karma sẽ được chạy với lệnh `ng test` trong của sổ terminal.

## Tại sao là Jasmine ?

Jasmine là một framework được phát triển để điều khiển hành vi phục vụ cho mục đính kiểm thử JavaScript code mà sẽ hoạt động tốt với Karma. Giống như Karma, nó cũng được khuyên sử dụng trong [tài liệu Angular](https://angular.io/guide/testing#setup) cũng như là cách cài đặt với Angular CLI. Jasmine cũng được cung cấp miễn phí và không yêu cầu DOM.

Tôi yêu Jasmine bởi vì hầu như tất cả những gì tôi cần đều đã được tích hợp. Một ví dụ đáng chú ý nhất là `spy`. `spy` trên một function cho phép bạn theo dõi những thuộc tính như là liệu nó có được goị hay không, gọi bao nhiêu lần, và với những đối số nào được gọi. Với framework như Mocha, `spy` sẽ không được tích hợp và phải kết hợp với một số thư việc khác như Sinon.js.

Một tin tốt là chi phí chuyển đổi giữa những framwork kiểm thử tương đối thấp với sự khác biệt nhỏ về cú pháp như Jasmine là `toEqual()` và Mocha là `to.equal()`.

## Một ví dụ kiểm thử đơn giản

Hãy tưởng tượng bạn có một người hầu ngoài hành tinh có tên là Adder sẽ đi với bạn khắp mọi nơi. Ngoài việc trở thành một người bạn đồng hành ngoài hành tinh dễ thương, Adder thực sự chỉ có thể làm một việc, cộng 2 số với nhau.

Để xác minh lại khả năng của Adder là cộng 2 số, chúng ta có thể tạo ra một tập các trường hợp kiểm thử để xem liệu Adder có cung cấp cho chúng ta câu trả lời chính xác hay không.

Với Jasmine, có thể bắt đầu với cái được gọi là [`suite`](https://jasmine.github.io/tutorials/your_first_suite) mà sẽ nhóm lại tập các trường hợp kiểm thử liên quan với nhau bằng cách gọi function `describe`.

```ts
// A Jasmine suite
describe('Adder', () => {

});
```

Từ đây chúng ta có thể cung cấp cho Adder với một tập các trường hợp kiểm thử như là hai số dương (2, 4), một số dương và số không (3, 0), một số dương và một số âm (5, -2) và nhiều trường hợp khác nữa.

Với Jasmine, tất cả chúng được gọi là [`specs`](https://jasmine.github.io/tutorials/your_first_suite) mà chúng ta tạo ra bằng cách gọi function `it`, truyền giá trị kiểu chuỗi cho nó để diễn tả chức năng mà nó đang được kiểm thử.

```ts
describe('Adder', () => {
  // A jasmine spec
  it('should be able to add two whole numbers', () => {
    expect(Adder.add(2, 2)).toEqual(4);
  });

  it('should be able to add a whole number and a negative number', () => {
    expect(Adder.add(2, -1)).toEqual(1);
  });

  it('should be able to add a whole number and a zero', () => {
    expect(Adder.add(2, 0)).toEqual(2);
  });
});
```

Bên trong mỗi spec chúng ta gọi `expect` và cung cấp cho nó cái gì đó gọi là `actual`. Sau sự mong đợi, hoặc `expect` là `matcher` function, như là `toEqual`, mà lập trình viên kiểm thử cung cấp với `giá trị mong đợi(expected output)` của code đang được kiểm thử.

> Còn rất nhiều `matcher` cho phép chúng ta sử dụng ngoài toEqual. Bạn có thể xem tất cả danh sách bên trong [tài liệu Jasmine](https://jasmine.github.io/api/edge/matchers.html).

Kiểm tra của chúng ta không quan tâm đến quá trình Adder `đạt được đến kết quả đó như thế nào`. Chúng ta chỉ quan tâm về câu trả lời của Adder cung cấp cho chúng ta. Cho tất cả những gì chúng ta biết, đây là quá trình Adder thực hiện việc `cộng`.

```ts
function add(first, second) {
  if (true) { // why?
    if (true) { // why??
      if (1 === 1) { // why?!?1
        return first + second;
      }
    }
  }
}
```

Nói cách khác, chúng ta chỉ quan tâm đến giá trị mong đợi mà Adder trả ra - chúng ta không quan tâm đến Adder thực hiện quá trình đó như thế nào.

## Cài đặt Angular

Chúng ta sẽ bắt đầu bằng cách tạo một ứng dụng mới sử dụng Angular CLI.

```
ng new angular-testing --routing
```

Khi chúng ta có nhiều màn hình trong ứng dụng, chúng ta sử dụng cờ `--routing`, CLI sẽ tự động tạo ra routing module cho chúng ta.

Tại đây chúng ta có thể xác nhận lại tất cả đã hoạt động chính xác bằng cách di chuyển vào thư mục `angular-testing` vừa mới tạo và chạy ứng dụng.

```
cd angular-tesing
ng serve -o
```

![Chạy ứng dụng Angular](assets/running-ng-serve.png)

Bạn cũng có thể xác minh hiện tại ứng dụng kiểm thử có đang vượt qua hết các trạng thái kiểm tra hay không.

```
ng test
```

## Thêm trang home

Trước khi tạo một service của trang home để thể hiện thông tin của user, chúng ta sẽ tạo component của trang home.

```
ng g component home
```

Bây giờ component của chúng ta đã được tạo ra, chúng ta có thể cập nhật routing module (`app-routing.module.ts`) tại thư mục root (`src/app`) tới `HomeComponent`.

```ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  { path: '', component: HomeComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Chạy ứng dụng bạn sẽ thấy dòng text "home works!" phía dưới cùng của template mặc định trong `app.component.html` mà đã được tạo ra bởi CLI.

## Xoá kiểm thử trong AppComponent

Chúng ta không cần nội dung mặc định của `AppComponent`, hãy cập nhật lại nó bằng cách xoá một số code không cần thiết.

Đầu tiên, xoá mọi thứ trong `app.component.html` và chỉ để lại `router-outlet` directive.

```html
<router-outlet></router-outlet>
```

Bên trong `app.component.ts`, bạn cũng nên xoá đi thuộc tính `title`.

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent { }
```

Cuối cùng, bạn cập nhật lại test trong `app.component.spec.ts` bằng cách xoá đi 2 test cho một số nội dung mà chúng ta đã xoá trước đó trong `app.component.html`.

```ts
import { async, TestBed } from '@angular/core/testing';
import { RouterTestingModule } from '@angular/router/testing';
import { AppComponent } from './app.component';
describe('AppComponent', () => {
  beforeEach(async(() => {
    TestBed.configureTestingModule({
      imports: [
        RouterTestingModule
      ],
      declarations: [
        AppComponent
      ],
    }).compileComponents();
  }));
  it('should create the app', async(() => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.debugElement.componentInstance;
    expect(app).toBeTruthy();
  }));
});
```

## Kiểm thử service Angular

Bây giờ trang home của chúng ta đã được thiết lập, chúng ta sẽ tạo service để thể hiện thư mục của nhân viên trong trang này.

```
ng g serive services/users/users
```

Tại đây chúng ta đã tạo `users` service bên trong thư mục mới `services/users` sẽ giữ services của chúng ta cách biệt với thư mục gốc `app` để tránh sự lộn xộn.

```ts
import { TestBed } from '@angular/core/testing';
import { UsersService } from './users.service';

describe('UsersService', () => {
  let usersService: UsersService; // Add this

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [UsersService]
    });

    usersService = TestBed.get(UsersService); // Add this
  });

  it('should be created', () => { // Remove inject()
    expect(usersService).toBeTruthy();
  });
});
```

Trong đoạn code phía trên, `TestBed.configureTestingModule({})` thiết lập service chúng ta muốn test với `UsersService` bên trong `providers`. Sau đó chúng ta inject service bên trong test suite sử dụng `TestBed.get()` với service chúng ta muốn test như là một đối số. Chúng ta thiết lập giá trị trả về tới biến `usersService` mà sẽ cho phép chúng ta tương tác với service này bên trong những trường hợp kiểm thử của chúng ta.

Bây giờ sẽ cấu trúc lại các trường hợp test của chúng ta, chúng ta thêm vào trường hợp test cho phương thức `all` mà sẽ trả về danh sách người dùng.

```ts
import { of } from 'rxjs'; // Add import

describe('UsersService', () => {
  ...

  it('should be created', () => {
    expect(usersService).toBeTruthy();
  });

  // Add tests for all() method
  describe('all', () => {
    it('should return a collection of users', () => {
      const userResponse = [
        {
          id: '1',
          name: 'Jane',
          role: 'Designer',
          pokemon: 'Blastoise'
        },
        {
          id: '2',
          name: 'Bob',
          role: 'Developer',
          pokemon: 'Charizard'
        }
      ];
      let response;
      spyOn(usersService, 'all').and.returnValue(of(userResponse));

      usersService.all().subscribe(res => {
        response = res;
      });

      expect(response).toEqual(userResponse);
    });
  });
});
```

Tại đây chúng ta thêm kết quả mong đợi cho test mà phương thức `all` sẽ 