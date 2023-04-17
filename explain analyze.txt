sql:
select
  cre.camp_id,
  cre.cdl,
  count(cre.id) as cnt
from
  cre
  join ui on cre.ui_id = ui.id
  join camp on cre.camp_id = camp.id
  left join adv_cre on cre.ui_id = adv_cre.ui_id
where
  ui.review_st = 2
  and cre.review_st in (2, 5, 6)
  and cre.put_st = 1
  and ui.put_st = 1
  and camp.put_st = 1
  and (
    adv_cre.put_st = 1
    or adv_cre.put_st is null
  )
  and cre.cst in (0, 1, 2, 3, 5, 6)
  and cre.cdl != 0
  and cre.camp_id in (123)
group by
  cre.camp_id,
  cre.cdl
  
explain analyze:
-> Table scan on <temporary>  (actual time=0.001..0.002 rows=2 loops=1)
    -> Aggregate using temporary table  (actual time=1409.291..1409.292 rows=2 loops=1)
        -> Filter: ((adv_cre.put_st = 1) or (adv_cre.put_st is null))  (cost=52371.63 rows=323) (actual time=568.137..1401.682 rows=4707 loops=1)
            -> Nested loop left join  (cost=52371.63 rows=323) (actual time=568.135..1400.022 rows=4707 loops=1)
                -> Nested loop inner join  (cost=51879.20 rows=323) (actual time=568.111..1394.805 rows=4707 loops=1)
                    -> Filter: ((ui.put_st = 1) and (ui.review_st = 2))  (cost=15208.80 rows=1342) (actual time=0.088..154.590 rows=56166 loops=1)
                        -> Table scan on ui  (cost=15208.80 rows=134213) (actual time=0.085..116.877 rows=162776 loops=1)
                    -> Filter: ((cre.camp_id = 1378813214) and (cre.put_st = 1) and (cre.review_st in (2,5,6)) and (cre.cst in (0,1,2,3,5,6)) and (cre.cdl <> 0))  (cost=23.76 rows=0) (actual time=0.020..0.022 rows=0 loops=56166)
                        -> Index lookup on cre using idx_ui_id (ui_id=ui.id)  (cost=23.76 rows=36) (actual time=0.006..0.019 rows=11 loops=56166)
                -> Single-row index lookup on adv_cre using PRIMARY (ui_id=ui.id)  (cost=1.00 rows=1) (actual time=0.001..0.001 rows=0 loops=4707)ex
