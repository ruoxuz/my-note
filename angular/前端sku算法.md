# 前端sku算法

这里先说下思路：

1. 根据规格列表（specList）创建邻接矩阵（数组）
2. 根据可选规格组合(specCombinationList)填写顶点的值
3. 获得所有可选顶点，然后根据可选顶点填写同级顶点的值

创建邻接矩阵
首先，我们需要提供一个类来创建邻接矩阵。一个邻接矩阵，首先需要传入一个顶点数组：vertex,需要一个用来装邻接矩阵的数组：adjoinArray。刚刚我们上面说到了，这个类还必须提供计算并集和交集的方法：

    export type AdjoinType = Array<string>;

    export default class AdjoinMatrix {
    vertex: AdjoinType; // 顶点数组
    quantity: number; // 矩阵长度
    adjoinArray: Array<number>; // 矩阵数组

    constructor(vertx: AdjoinType) {
        this.vertex = vertx;
        this.quantity = this.vertex.length;
        this.adjoinArray = [];
        this.init();
    }
    // 初始化数组
    init() {
        this.adjoinArray = Array(this.quantity * this.quantity).fill(0);
    }

    /*
    * @param id string
    * @param sides Array<string>
    *  传入一个顶点，和当前顶点可达的顶点数组，将对应位置置为1
    */
    setAdjoinVertexs(id: string, sides: AdjoinType) {
        const pIndex = this.vertex.indexOf(id);
        sides.forEach((item) => {
        const index = this.vertex.indexOf(item);
        this.adjoinArray[pIndex * this.quantity + index] = 1;
        });
    }

    /*
    * @param id string
    * 传入顶点的值，获取该顶点的列
    */
    getVertexCol(id: string) {
        const index = this.vertex.indexOf(id);
        const col: Array<number> = [];
        this.vertex.forEach((item, pIndex) => {
        col.push(this.adjoinArray[index + this.quantity * pIndex]);
        });
        return col;
    }

    /*
    * @param params Array<string>
    * 传入一个顶点数组，求出该数组所有顶点的列的合
    */
    getColSum(params: AdjoinType) {
        const paramsVertex = params.map((id) => this.getVertexCol(id));
        const paramsVertexSum: Array<number> = [];
        this.vertex.forEach((item, index) => {
        const rowtotal = paramsVertex
            .map((value) => value[index])
            .reduce((total, current) => {
            total += current || 0;
            return total;
            }, 0);
        paramsVertexSum.push(rowtotal);
        });
        return paramsVertexSum;
    }

    /*
    *  @param params Array<string>
    * 传入一个顶点数组，求出并集
    */
    getCollection(params: AdjoinType) {
        const paramsColSum = this.getColSum(params);
        let collections: AdjoinType = [];
        paramsColSum.forEach((item, index) => {
        if (item && this.vertex[index]) collections.push(this.vertex[index]);
        });
        return collections;
    }

    /*
    *  @param params Array<string>
    * 传入一个顶点数组，求出交集
    */
    getUnions(params: AdjoinType) {
        const paramsColSum = this.getColSum(params);
        let unions: AdjoinType = [];
        paramsColSum.forEach((item, index) => {
        if (item >= params.length && this.vertex[index]) unions.push(this.vertex[index]);
        });
        return unions;
    }
    }

有了这个类，接下来可以创建一个专门用于生成商品多规格选择的类，它继承于AdjoinMatrix。
创建多规格选择邻接矩阵
我们这个多规格选择的邻接矩阵，需要提供一个查询可选顶点的方法：getSpecscOptions

    import AdjoinMatrix from "./adjoin-martix";
    import { AdjoinType } from "./adjoin-martix";
    import { SpecCategoryType, CommoditySpecsType } from "../redux/reducer/spec-reducer";

    export default class SpecAdjoinMatrix extends AdjoinMatrix {
    specList: Array<CommoditySpecsType>;
    specCombinationList: Array<SpecCategoryType>;

    constructor(specList: Array<CommoditySpecsType>, specCombinationList: Array<SpecCategoryType>) {
        super(specList.reduce((total: AdjoinType, current) => [...total, ...current.list], []));
        this.specList = specList;
        this.specCombinationList = specCombinationList;
        // 根据可选规格列表矩阵创建
        this.initSpec();
        // 同级顶点创建
        this.initSameLevel();
    }

    /**
    * 根据可选规格组合填写邻接矩阵的值
    */
    initSpec() {
        this.specCombinationList.forEach((item) => {
        this.fillInSpec(item.specs);
        });
    }
    // 填写同级点
    initSameLevel() {
        // 获得初始所有可选项
        const specsOption = this.getCollection(this.vertex);
        this.specList.forEach((item) => {
        const params: AdjoinType = [];
        // 获取同级别顶点
        item.list.forEach((value) => {
            if (specsOption.includes(value)) params.push(value);
        });
        // 同级点位创建
        this.fillInSpec(params);
        });
    }
    /*
    * 传入顶点数组，查询出可选规格
    * @param params
    */
    getSpecscOptions(params: AdjoinType) {
        let specOptionCanchoose: AdjoinType = [];
        if (params.some(Boolean)) {
        // 过滤一下选项
        specOptionCanchoose = this.getUnions(params.filter(Boolean));
        } else {
        // 所有可选项
        specOptionCanchoose = this.getCollection(this.vertex);
        }
        return specOptionCanchoose;
    }

    /*
    * @params
    * 填写邻接矩阵的值
    */
    fillInSpec(params: AdjoinType) {
        params.forEach((param) => {
        this.setAdjoinVertexs(param, params);
        });
    }
    }

页面渲染
好了到了这一步，我们已经可以在页面中使用这两个类了：

    import React, { useState, useMemo } from "react";
    import { useSelector } from "react-redux";
    import { RootState } from "../redux/reducer/root-reducer";
    import SpecAdjoinMatrix from "../utils/spec-adjoin-martix";
    import "./spec.css";
    const classNames = require("classnames");

    const Spec: React.FC = () => {
    const { specList, specCombinationList } = useSelector((state: RootState) => state.spec);
    // 已选择的规格，长度为规格列表的长度
    const [specsS, setSpecsS] = useState(Array(specList.length).fill(""));

    // 创建一个规格矩阵
    const specAdjoinMatrix = useMemo(() => new SpecAdjoinMatrix(specList, specCombinationList), [specList, specCombinationList]);
    // 获得可选项表
    const optionSpecs = specAdjoinMatrix.getSpecscOptions(specsS);

    const handleClick = function(bool: boolean, text: string, index: number) {
        // 排除可选规格里面没有的规格
        if (specsS[index] !== text && !bool) return;
        // 根据text判断是否已经被选中了
        specsS[index] = specsS[index] === text ? "" : text;
        setSpecsS(specsS.slice());
    };

    return (
        <div className="container">
        {specList.map(({ title, list }, index) => (
            <div key={index}>
            <p className="title">{title}</p>
            <div className="specBox">
                {list.map((value, i) => {
                const isOption = optionSpecs.includes(value); // 当前规格是否可选
                const isActive = specsS.includes(value); // 当前规格是否被选
                return (
                    <span
                    key={i}
                    className={classNames({
                        specOption: isOption,
                        specAction: isActive,
                        specDisabled: !isOption,
                    })}
                    onClick={() => handleClick(isOption, value, index)}
                    >
                    {value}
                    </span>
                );
                })}
            </div>
            </div>
        ))}
        </div>
    );
    };

    export default Spec;

下面再附上angular的实现：

 adjoin-matrix.ts

    export class AdjoinMatrix {
    vertex: AdjoinType; // 顶点数组
    quantity: number; // 矩阵长度
    adjoinArray: Array<number>; // 矩阵数组

    constructor(vertex: AdjoinType) {
        this.vertex = vertex;
        this.quantity = this.vertex.length;
        this.adjoinArray = [];
        this.init();
    }
    // 初始化数组
    init() {
        this.adjoinArray = Array(this.quantity * this.quantity).fill(0);
    }

    /**
    * @param id string
    * @param sides Array<string>
    *  传入一个顶点，和当前顶点可达的顶点数组，将对应位置置为1
    */
    setAdjoinVertexes(id: string, sides: AdjoinType) {
        const pIndex = this.vertex.indexOf(id);
        sides.forEach((item) => {
        const index = this.vertex.indexOf(item);
        this.adjoinArray[pIndex * this.quantity + index] = 1;
        });
    }

    /**
    * @param id string
    * 传入顶点的值，获取该顶点的列
    */
    getVertexCol(id: string) {
        const index = this.vertex.indexOf(id);
        const col: Array<number> = [];
        this.vertex.forEach((item, pIndex) => {
        col.push(this.adjoinArray[index + this.quantity * pIndex]);
        });
        return col;
    }

    /**
    * @param params Array<string>
    * 传入一个顶点数组，求出该数组所有顶点的列的合
    */
    getColSum(params: AdjoinType) {
        const paramsVertex = params.map((id) => this.getVertexCol(id));
        const paramsVertexSum: Array<number> = [];
        this.vertex.forEach((item, index) => {
        const rowtotal = paramsVertex
            .map((value) => value[index])
            .reduce((total, current) => {
            total += current || 0;
            return total;
            }, 0);
        paramsVertexSum.push(rowtotal);
        });
        return paramsVertexSum;
    }

    /**
    *  @param params Array<string>
    * 传入一个顶点数组，求出并集
    */
    getCollection(params: AdjoinType) {
        const paramsColSum = this.getColSum(params);
        let collections: AdjoinType = [];
        paramsColSum.forEach((item, index) => {
        if (item && this.vertex[index]) collections.push(this.vertex[index]);
        });
        return collections;
    }

    /**
    *  @param params Array<string>
    * 传入一个顶点数组，求出交集
    */
    getUnions(params: AdjoinType) {
        const paramsColSum = this.getColSum(params);
        let unions: AdjoinType = [];
        paramsColSum.forEach((item, index) => {
        if (item >= params.length && this.vertex[index]) unions.push(this.vertex[index]);
        });
        return unions;
    }
    }

    export type AdjoinType = Array<string>;

spec-adjoin-matrix.ts

    import {AdjoinMatrix, AdjoinType} from "./adjoin-matrix";
    import {Sku} from "./sku";
    import {Property} from "./property";

    export class SpecAdjoinMatrix extends AdjoinMatrix{
    specList: Array<Property>;
    specCombinationList: Array<Sku>;

    constructor(specList: Array<Property>, specCombinationList: Array<Sku>) {
        super(specList.reduce((accumulator: AdjoinType, currentValue) =>
        [...accumulator, ...currentValue.propertyValueList.map(value => value.propertyValueId)], []));
        this.specList = specList;
        this.specCombinationList = specCombinationList;
        this.initSpec();
        this.initSameLevel();
    }

    /**
    * 根据可选规格组合填写邻接矩阵的值
    */
    initSpec() {
        this.specCombinationList.forEach((item) => {
        this.fillInSpec(item.propertyList.map(value => value.propertyValueId));
        });
    }

    /**
    * 填写同级点
    */
    initSameLevel() {
        // 获得初始所有可选项
        const specsOption = this.getCollection(this.vertex);
        this.specList.forEach((item) => {
        const params: AdjoinType = [];
        // 获取同级别顶点
        item.propertyValueList.forEach((value) => {
            if (specsOption.includes(value.propertyValueId)) params.push(value.propertyValueId);
        });
        // 同级点位创建
        this.fillInSpec(params);
        });
    }

    /**
    * 传入顶点数组，查询出可选规格
    * @param params
    */
    getSpecscOptions(params: AdjoinType) {
        let specOptionCanchoose: AdjoinType = [];
        if (params.some(Boolean)) {
        // 过滤一下选项
        specOptionCanchoose = this.getUnions(params.filter(Boolean));
        } else {
        // 所有可选项
        specOptionCanchoose = this.getCollection(this.vertex);
        }
        return specOptionCanchoose;
    }

    getSpecCombinations(params: AdjoinType) {
        let selectedSpec: Array<Sku> = [];
        if (params.some(Boolean)) {
        selectedSpec = this.specCombinationList;
        let nonEmptyParams = params.filter(item => item !== '');
        nonEmptyParams.forEach(param => {
            selectedSpec = selectedSpec.filter((item, index, array) => {
            return item.propertyList.map(item => item.propertyValueId).includes(param);
            });
        });
        } else {
        selectedSpec = this.specCombinationList;
        }
        return selectedSpec;
    }

    /**
    * @params
    * 填写邻接矩阵的值
    */
    fillInSpec(params: AdjoinType) {
        params.forEach((param) => {
        this.setAdjoinVertexes(param, params);
        });
    }
    }

页面渲染

    @ViewChildren("chip") chipList!: QueryList<MatChip>;
    propertyClick(chip: MatChip) {
        chip.toggleSelected();
        // console.log(chip.value);
        this.selectedProperties = this.chipList.map(c => c.selected ? c.value : '');
        // console.log(this.selectedProperties);
        this.optionSpecs = this.specAdjoinMatrix.getSpecscOptions(this.selectedProperties);
        // console.log(this.optionSpecs);
        this.chipList.forEach(item => {
        if (this.optionSpecs.includes(item.value)) {
            item.disabled = false;
            item.selectable = true;
        } else {
            item.disabled = true;
            item.selectable = false;
        }
        });
        this.selectedSku = this.specAdjoinMatrix.getSpecCombinations(this.selectedProperties);
        // console.log(this.selectedSku);
    }

html

    <div class="product-sku row" *ngFor="let propertyType of itemDetail?.propertyList">
    <div class="col-2 product-centered">
        <label class="product-text-justify"
            id="{{propertyType.propertyId}}">{{propertyType.propertyName}}</label>
    </div>
    <mat-chip-list class="col-10" multiple="false">
        <mat-chip #chip="matChip" *ngFor="let propertyValueType of propertyType.propertyValueList"
                [value]="propertyValueType.propertyValueId"
                (click)="propertyClick(chip)">
        {{propertyValueType.propertyValueName}}
        </mat-chip>
    </mat-chip-list>
    </div>

[分分钟学会前端sku算法（商品多规格选择）](https://juejin.cn/post/6844904196349640718)
