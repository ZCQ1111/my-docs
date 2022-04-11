<!--
 * @Author: Null Zhao
 * @Date: 2022-03-04 10:07:56
 * @LastEditors: Null Zhao
 * @LastEditTime: 2022-03-11 14:18:05
 * @FilePath: \my-docs\docs\lb-controller.md
 * @Description: 
 * ctrl+alt+i ctrl+win+t win+y
 * Copyright (c) 2022 by null, All Rights Reserved. 
-->
### import
```
import {Filter, Where} from '@loopback/repository';
import {
  createStubInstance,
  expect,
  sinon,
  StubbedInstanceWithSinonAccessor
} from '@loopback/testlab';
import {TestController} from '../../../controllers';
import {Test} from '../../../models/test.model';
import {TestRepository} from '../../../repositories';
import {TestService} from '../../../services';
import {givenTest} from '../../helpers';
```
### 定义
```
let repository: StubbedInstanceWithSinonAccessor<TestRepository>;
  let controller: TestController;
  let testSvc: StubbedInstanceWithSinonAccessor<TestService>;
  let findtest:  sinon.SinonStub;
  let aTest: Test;
  let aTestWithId: Test;
  let aChangedTest: Test;
  let aListOfTests: Test[];
```
### beforeEach
```
beforeEach(givenStubbedRepository);
function givenStubbedRepository() {
    repository = createStubInstance(TestRepository);
    aTest = givenTest();
    aTestWithId = givenTest();
    aListOfTests = [
      aTestWithId,
      givenTest(),
    ] as Test[];
    aChangedTest = givenTest();
    testSvc = createStubInstance(TestService);
    findtest = testSvc.find as sinon.SinonStub;
    controller = new TestController(repository, testSvc);
  }
```
### test 
```
describe('findTests', () => {
    it('uses the provcolumn1ed filter', async () => {
      //arrange
      const find = repository.stubs.find;
      const filter: Filter<Test> = {where: {column1: 'row1'}};
      //act&assert
      find.resolves(aListOfTests);
      await controller.find(filter);
      sinon.assert.calledWith(find, filter);
    });

    it('findtest test', async () => {
      const where: Where<Test> = {column1: 'row1'};
      findtest.resolves([aListOfTests]);
      await controller.findtest(where);
      sinon.assert.calledWith(findtest, where);
    });
  });
```
### `helpers.ts`
```
import {Test} from '../models';
export function givenTest(test?: Partial<Test>) {
  const data = Object.assign(
    { column1: 'row1', column2: 0, column3: new Date(1995, 11, 17) },
    test,
  );
  return new Test(data);
}
```